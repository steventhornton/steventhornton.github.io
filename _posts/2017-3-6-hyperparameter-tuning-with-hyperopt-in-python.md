---
title: "Hyperparameter Tuning with hyperopt in Python"
categories:
  - Blog
excerpt: "Hyperparameter tuning is an important step for maximizing the performance of a model. Several Python packages have been developed specifically for this purpose. Scikit-learn provides a few options, GridSearchCV and RandomizedSearchCV being two of the more popular options. Outside of scikit-learn, the Optunity, Spearmint and hyperopt packages are all designed for optimization. In this post, I will focus on the hyperopt package which provides algorithms that are able to outperform randomized search and can find results comparable to a grid search while fitting substantially less models."
---

{: .notice--info}
<a href="https://github.com/steventhornton/Hyperparameter-Tuning-with-hyperopt-in-Python" target="_blank">The full code is available on Github.</a>

Hyperparameter tuning is an important step for maximizing the performance of a model. Several Python packages have been developed specifically for this purpose. Scikit-learn provides a <a href="http://scikit-learn.org/stable/modules/classes.html#hyper-parameter-optimizers" target="_blank">few options</a>, `GridSearchCV` and `RandomizedSearchCV` being two of the more popular options. Outside of scikit-learn, the <a href="https://pypi.python.org/pypi/Optunity" target="_blank">Optunity</a>, <a href="https://github.com/JasperSnoek/spearmint" target="_blank">Spearmint</a> and <a href="http://hyperopt.github.io/hyperopt/" target="_blank">hyperopt</a> packages are all designed for optimization. In this post, I will focus on the hyperopt package which provides algorithms that are able to outperform randomized search and can find results comparable to a grid search while fitting substantially less models.

The IMBD movie review sentiment classification problem will be used as an illustrative example. This dataset appears in a Kaggle tutorial <a href="https://www.kaggle.com/c/word2vec-nlp-tutorial" target="_blank">Bag of Words Meets Bags of Popcorn</a>. The purpose of this post is not to achieve record breaking performance for this classification model; many people have already taken care of that (see the Kaggle <a href="https://www.kaggle.com/c/word2vec-nlp-tutorial/discussion" target="_blank">discussion</a>). Instead, I will discuss how you can use the hyperopt package to automate the hyperparamter tuning process. The ideas here are not limited to models built using scikit-learn, they can easily be adapted to any optimization problem. For models built with scikit-learn, hyperopt provides a packaged called <a href="http://hyperopt.github.io/hyperopt-sklearn/" target="_blank">hyperopt-sklearn</a> that is designed to work with scikit-learn models. I will not be discussing this package here, rather I will work with the base hyperopt package which allows greater flexibility.

The model I will use is a fairly simple model for text classification consisting of building a term frequency–inverse document frequency (TF-IDF) matrix from the corpus of training texts, then training the <a href="http://scikit-learn.org/stable/modules/generated/sklearn.linear_model.SGDClassifier.html" target="_blank">`SGDClassifier`</a> from scikit-learn with modified-huber loss on the corpus. The scoring metric I will use to evaluate the performance of the model is the area under the receiver operating characteristic curve (ROC AUC) as this is used in the Kaggle tutorial. Training this model with the default hyperparameters already provides a good result with an AUC score of 0.9519 on the test data. Using the procedure that follows I was able to improve the performance to an AUC score of 0.9621.

## Data Preprocessing
The data for this tutorial can be downloaded from the Kaggle tutorial <a href="https://www.kaggle.com/c/word2vec-nlp-tutorial/data" target="_blank">here</a>. You should download the `labeledTrainData.tsv.zip` and `testData.tsv.zip` files. The unlabeled data will not be used.

First, we need to load our data into a Pandas dataframe and add columns for the score (the rating of the review out of 10) and for the class (1 for positive, and 0 for negative). The test data will be sorted by class, this will be needed later when plotting the ROC curve.

{% highlight python %}
import pandas as pd
import numpy as np

# Load the data
df_train = pd.read_table('labeledTrainData.tsv', sep='\t')
df_test = pd.read_table('testData.tsv', sep='\t')

# Add a column for the score (out of 10)
df_train['score'] = df_train['id'].map(lambda x: int(x.split('_')[1]))
df_test['score'] = df_test['id'].map(lambda x: int(x.split('_')[1]))

# Add a column for the class, 1 for positive, 0 for negative
df_train['class'] = df_train['score'].map(lambda x: int(x > 5))
df_test['class'] = df_test['score'].map(lambda x: int(x > 5))

# Sort the df_test by class (it will need to be sorted later)
df_test = df_test.sort_values('class', ascending=False)
{% endhighlight %}

The next step in any text classification problem is cleaning the documents.

{% highlight python %}
from nltk.corpus import stopwords
cachedStopWords = stopwords.words('english')

# Function for removing stopwords from a string
def removeStopwords(s):
    return ' '.join([word for word in s.split() if word not in cachedStopWords])

# Function for cleaning the reviews
def cleanText(s):
    s = s.str.lower()                         # Convert to lowercase
    s = s.str.replace(r'<.*?>', ' ')          # Remove HTML characters
    s = s.str.replace('\'', '')               # Remove single quotes ' 
    s = s.str.replace('-', '')                # Remove dashes -
    s = s.str.replace(r'[^a-zA-Z]', ' ')      # Remove non alpha characters
    s = s.map(lambda s: ' '.join(s.split()))  # Remove extra whitespae
    s = s.str.strip()                         # Remove whitespace at start and end
    s = s.apply(lambda x: removeStopwords(x)) # Remove stopwords
    return s

# Clean the reviews
df_train['review'] = cleanText(df_train['review'])
df_test['review'] = cleanText(df_test['review'])
{% endhighlight %}

When we build the model later the `TfidfVectorizer` in `sklearn.feature_extraction.text` will be used to construct a TF-IDF matrix. This function is able to do the preprocessing of the text, I avoid doing that here because the TF-IDF matrix will need to be reconstructed for each iteration of the hyperparamter tuning process. By processing the documents before building the TF-IDF matrix we will avoid repeating this work.

Finally, we separate the reviews and their classes into separate numpy arrays.

{% highlight python %}
# Separate into training and test data
X_train = df_train['review']
y_train = df_train['class']
X_test = df_test['review']
y_test = df_test['class']
{% endhighlight %}

## The Model
Now that the data is cleaned, we can begin building the model. The model will consist of 3 parts:

- Construct a matrix of term frequency–inverse document frequency (TF-IDF) features from the corpus of movie reviews
- Select the features with the highest values for the chi-squared statistic from the training data
- Train a linear model using modified-huber loss with stochastic gradient descent (SGD) learning

The SGDClassifier will use modified-huber loss with an elasticnet penalty. The elasticnet penalty will allow a convex combination of L1 and L2 penalties. The SGDClassifier has a hyperparameter called `l1_ratio` that controls how much the L1 penalty is used. This will be one of the hyperparamters that will be tuned. This will allow the hyperparamter tuning to select between using L1 or L2 penalty or a combination of the two.

A <a href="http://scikit-learn.org/stable/modules/generated/sklearn.pipeline.Pipeline.html" target="_blank">pipeline</a> will be used to make fitting and testing the model easier.

{% highlight python %}
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.linear_model import SGDClassifier
from sklearn.feature_selection.univariate_selection import SelectPercentile, chi2
from sklearn.pipeline import Pipeline

# The model
vectorizer = TfidfVectorizer(strip_accents='unicode')
kbest = SelectPercentile(chi2)
clf = SGDClassifier(loss='modified_huber', penalty='elasticnet')

pipe = Pipeline([('vec', vectorizer),
                 ('kbest', kbest),
                 ('clf', clf)])
{% endhighlight %}

Before we begin the hyperparameter tuning process, we will train the model with the default parameters. The AUC score of the test data with the default hyperparameters is 0.9523. The ROC curve is plotted in Figure 1 below.

<figure class="align-center" style="width: 600px;">
	<a name="Figure1"></a>
	<img src="{{ site.asset_url }}/Hyperparameter_Tuning_with_hyperopt_in_Python/Figure1.png"    
		 srcset="{{ site.asset_url }}/Hyperparameter_Tuning_with_hyperopt_in_Python/Figure1.png 1x, {{ site.asset_url }}/Hyperparameter_Tuning_with_hyperopt_in_Python/Figure1%402x.png 2x" 
		 alt="ROC curve with default hyperparameter values on test data." 
		 width="600" 
		 height="400">
	<figcaption><b>Figure 1:</b> The ROC curve for the model when using the default parameter values give an AUC score of 0.952 on the held-out test data.
	</figcaption>
</figure>

## Hyperparameter Tuning

We are now ready to set up hyperopt to optimize the ROC AUC. hyperopt works by minimizing an objective function and sampling parameter values from various distributions. Since the goal is to maximize the AUC score, we will have hyperopt minimize $1 - \text{AUC}$.

10-fold cross-validation will be used in for hyperparamter tuning to avoid overfitting to the training data. For each iteration of hyperparamter tuning, the folds will be randomized, again to avoid overfitting to the 10-folds selected.

Here we will tune 8 hyperparamters:

<table class="set-table">
    <thead>
        <tr>
            <th>Parameter Name</th>
            <th>Function</th>
            <th>Description</th>
            <th>Range</th>
        </tr>
    </thead>
<tbody>
</tbody>
    <tr>
        <td><code>ngram_range</code></td>
        <td><code>TfidfVectorizer</code></td>
        <td>The range of n-grams to use. <code>(1,1)</code> corresponds to only unigrams, and <code>(1,3)</code> would be unigrams, 2-grams and 3-grams.</td>
        <td><code>(1,1)</code>, <code>(1,2)</code>, <code>(1,3)</code></td>
    </tr>
    <tr>
        <td><code>min_df</code></td>
        <td><code>TfidfVectorizer</code></td>
        <td>The minimum number (or frequency) of documents a term must appear in to be included.</td>
        <td>1, 2, 3, or 4 documents</td>
    </tr>
    <tr>
        <td><code>max_df</code></td>
        <td><code>TfidfVectorizer</code></td>
        <td>The maximum number (or frequency) of documents a term must appear in to be included.</td>
        <td>Uniformly distributed between 70% and 100%</td>
    </tr>
    <tr>
        <td><code>sublinear_tf</code></td>
        <td><code>TfidfVectorizer</code></td>
        <td>Applies sublinear term-frequency scaling (i.e. <code>tf = 1 + log(tf)</code>)</td>
        <td><code>True</code> or <code>False</code></td>
    </tr>
    <tr>
        <td><code>percentile</code></td>
        <td><code>SelectPercentile</code></td>
        <td>The percentile score for the chi-squared statistic, any feature not in this percentile is not used</td>
        <td>Uniformly distributed between 50% and 100%</td>
    </tr>
    <tr>
        <td><code>alpha</code></td>
        <td><code>SGDClassifier</code></td>
        <td>The learning rate. The `SGDClassifier` documentation recommends this value be in the range $10^{-7}$. See http://scikit-learn.org/stable/modules/sgd.html#tips-on-practical-use</td>
        <td>Log-uniformly distributed between $10^{-9}$ and $10^{-4}$</td>
    </tr>
    <tr>
        <td><code>n_iter</code></td>
        <td><code>SGDClassifier</code></td>
        <td>Number of passes on the data, a value of <code>1e6/n_samples</code> is recommended.</td>
        <td>Random integer between 20 and 80 that is divisible by 5.</td>
    </tr>
    <tr>
        <td><code>l1_ratio</code></td>
        <td><code>SGDClassifier</code></td>
        <td>The elastic net mixing parameter. Penalty is <code>(1 - l1_ratio) * L2 + l1_ratio * L1</code>.</td>
        <td>Uniform between 0 and 1</td>
    </tr>
</table>

hyperopt provides several functions for defining various distributions. A full list can be found <a href="https://github.com/hyperopt/hyperopt/wiki/FMin#21-parameter-expressions" target="_blank">here</a>. Each hyperparameter is sampled from a distribution. To define the parameter space a dictionary is used where they keys start with the name of the function in the pipeline, followed by a double underscore, then the name of the hyperparameter for that function. This allows us to use the `set_params` function of the pipeline easily. Each of the functions in hyperopt also requires a name, I have used the same names for simplicity.

{% highlight python %}

from hyperopt import hp

# Parameter search space
space = {}

# One of (1,1), (1,2), or (1,3)
space['vec__ngram_range'] = hp.choice('vec__ngram_range', [(1,1), (1,2), (1,3)])

# Random integer in [1,3]
space['vec__min_df'] = 1+hp.randint('vec__min_df', 3)

# Uniform between 0.7 and 1
space['vec__max_df'] = hp.uniform('vec__max_df', 0.7, 1.0)

# One of True or False
space['vec__sublinear_tf'] = hp.choice('vec__sublinear_tf', [True, False])

# Random number between 50 and 100
space['kbest__percentile'] = hp.uniform('kbest__percentile', 50, 100)

# Random number between 0 and 1
space['clf__l1_ratio'] = hp.uniform('clf__l1_ratio', 0.0, 1.0)

# Log-uniform between 1e-9 and 1e-4
space['clf__alpha'] = hp.loguniform('clf__alpha', -9*np.log(10), -4*np.log(10))

# Random integer in 20:5:80
space['clf__n_iter'] = 20 + 5*hp.randint('clf__n_iter', 12)
{% endhighlight %}

Now that the hyperparaemter space has been defined we need to define an objective function for hyperopt to minimize. The objective function will start by randomly splitting the training data into 10 folds. The `KFold` function is used to ensure we get a new, random set of folds each time we call the objective function (otherwise we might overfit to the folds). The `cross_val_score` function with the `roc_auc` scoring metric is then called to run 10-fold cross-validation on the model. Since our goal is to maximize the ROC AUC, and hyperopt works by minimizing the objective function, we will return `1 - score.mean()` from the objective function. To significantly decrease the execution time, you can set the `n_jobs` parameter in the `cross_val_score` function.

I will note that I am using several of the variables as global variables here. There are a few ways to avoid this, you could either reload the data each time the objective function is called, or you could use a generator so the function loads the data once. When I initially drafted the code for this article I used a generator, although things became more complicated than I wanted for the tutorial as hyperopt will not work with generators. You can make hyperopt work with generators by setting up a simple producer-consumer model between two functions, one which sends new hyperparameter values to the consumer, and then returns the new average loss.

{% highlight python %}
from sklearn.model_selection import cross_val_score
from sklearn.model_selection import KFold

def objective(params):
    pipe.set_params(**params)
    shuffle = KFold(n_splits=10, shuffle=True)
    score = cross_val_score(pipe, X_train, y_train, cv=shuffle, scoring='roc_auc', n_jobs=1)
    return 1-score.mean()
{% endhighlight %}

We are now ready to use hyperopt to find optimal hyperparameter values. Here I will use the `fmin` function from hyperopt. I will also use the `Trials` object to store information about each iteration. This is useful for processing the information from each iteration afterwards. Here I have set it to run for 10 iterations for illustrative purposes.

{% highlight python %}
from hyperopt import fmin, tpe, Trials

# The Trials object will store details of each iteration
trials = Trials()

# Run the hyperparameter search using the tpe algorithm
best = fmin(objective,
            space,
            algo=tpe.suggest,
            max_evals=10,
            trials=trials)
{% endhighlight %}

After hyperopt is done, you can train the model using the entire training set and test it on the test data. Note the use of the `space_eval` function. You __must__ use this to get the optimal hyperparameter values. The `best` dictionary might look like it contains parameters, but it simply contains the encoding used by hyperopt to get the parameter values from the `space` dictionary.

{% highlight python %}
from hyperopt import space_eval

# Get the values of the optimal parameters
best_params = space_eval(space, best)

# Fit the model with the optimal hyperparamters
pipe.set_params(**best_params)
pipe.fit(X_train, y_train);

# Score with the test data
y_score = pipe.predict_proba(X_test)
auc_score = roc_auc_score(y_test, y_score[:,1])
{% endhighlight %}

## Results

Running this for 1000 iterations found an optimal training AUC score of 0.9672 corresponding to a test AUC of 0.9621. Using 1000 iterations was easily overkill as the best training AUC score found in the first 100 iterations was 0.9662, so little was gained by running the other 900 iterations. I ran the hyperparameter search on an AWS EC2 c4.4xlarge instance with the `n_jobs` parameter for `cross_val_score` set to 10 (i.e. each fold is trained in parallel). Execution took just under 15 hours. The ROC curve corresponding to a training AUC score of 0.9672 along with the ROC curve with no hyperparameter tuning can be seen in Figure 2 below.

<figure class="align-center" style="width: 600px;">
	<a name="Figure2"></a>
	<img src="{{ site.asset_url }}/Hyperparameter_Tuning_with_hyperopt_in_Python/Figure2.png"    
		 srcset="{{ site.asset_url }}/Hyperparameter_Tuning_with_hyperopt_in_Python/Figure2.png 1x, {{ site.asset_url }}/Hyperparameter_Tuning_with_hyperopt_in_Python/Figure2%402x.png 2x" 
		 alt="ROC curve with default and tuned hyperparameter values on test data." 
		 width="600" 
		 height="400">
	<figcaption><b>Figure 2:</b> The ROC curve for the model when using the default and optimized hyperparameter values give an AUC score of 0.952 and 0.962 respectively on the held-out test data.
	</figcaption>
</figure>

The `parameter_values.csv` file available on the <a href="https://github.com/steventhornton/Hyperparameter-Tuning-with-hyperopt-in-Python" target="_blank">GitHub repository</a> that complements this post contains the parameter values used at each of the 1000 iterations. Plotting each of these parameter values against the iteration gives some interesting results. Figure 3 shows the hyperparameter values that hyperopt sampled at each iteration. Many of the hyperparameters converge to regions surrounding their optimal values within the first 200 iterations. In the first 100 iterations, all of the hyperparameters appear to be randomly sampled before things begin to converge.

<figure class="align-center" style="width: 800px;">
	<img src="{{ site.asset_url }}/Hyperparameter_Tuning_with_hyperopt_in_Python/Figure3.png"    
		 srcset="{{ site.asset_url }}/Hyperparameter_Tuning_with_hyperopt_in_Python/Figure3.png 1x, {{ site.asset_url }}/Hyperparameter_Tuning_with_hyperopt_in_Python/Figure3%402x.png 2x" 
		 alt="" 
		 width="800" 
		 height="540">
	<figcaption><b>Figure 3:</b> Hyperparameter values sampled during hyperparameter optimization for 1000 iterations.
	</figcaption>
</figure>


## Final Thoughts
Overall, hyperopt provides a nice way to perform automated hyperparameter tuning without the cost associated with a grid search. The optimal hyperparameters should be better than what a randomized search will find as the hyperparamters seem to converge to values that result in improved results. Using hyperopt is fairly easy once understand how it is set up. Although hyperopt will converge to optimal values for the hyperparameters, it is still important to have a good understanding of the expected range of values for your hyperparamters.
