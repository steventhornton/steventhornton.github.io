---
title:  "Bohemian Eigenvalues"
categories:
  - Blog
excerpt: "Bohemian eigenvalues are the distribution of the eigenvalues of a random matrix. More specifically, this article explores matrices of low dimension (typically no larger than 10 x 10) whose entries are integers of bounded height. The name \"Bohemian\" is intended as a mnemonic and is derived from \"Bounded Height Integer Matrix Eigenvalues\" (BHIME). Here I present an overview of some preliminary results arising from this project."
---

{: .notice--info}
A repository with some of the code described here can be found on GitHub: <a href="https://github.com/BohemianMatrices/BHIME-Project" target="_blank">BHIME-Project</a>.

__Bohemian eigenvalues__ are the distribution of the eigenvalues of a random matrix. More specifically, this article explores matrices of low dimension (typically no larger than $10 \\times 10$) whose entries are integers of bounded height. The name "Bohemian" is intended as a mnemonic and is derived from "Bounded Height Integer Matrix Eigenvalues" (BHIME). Here I present an overview of some preliminary results arising from this project.

The __Bohemian Eigenvalue Project__ originated in 2014 while I was enrolled in a graduate course on experimental mathematics led by my PhD supervisor <a href="http://publish.uwo.ca/~rcorless/" target="_blank">Rob Corless</a>. One of the initial goals was to visualize the distributions of eigenvalues of random structured matrices. As the project evolved, many different types of distributions were explored, as I discuss here.

Before I jump into the details of the project, I present one of my favourite examples, and one of many interesting images that have come from this project. Consider a random $5 \times 5$ matrix where the entries are drawn uniformly from the set $\\{-1, 0, 1\\}$. This distribution contains $3^{25} = 847,\\!288,\\!609,\\!443$ different matrices. This will be used as a running example throughout the remainder of this article. <a href="#Figure1" >Figure 1</a> is a density plot of the $7,963,249$ distinct Bohemian eigenvalues from this set of matrices. Details on how this image was produced, and how I know the exact number of eigenvalues, can be found in the <a href="#Computation" >computation section</a>.

<figure class="align-center" style="width: 700px;">
    <a name="Figure1"></a>
    <img src="{{ site.asset_url }}/Bohemian_Eigenvalues/Figure1.png"    
         srcset="{{ site.asset_url }}/Bohemian_Eigenvalues/Figure1.png 1x, {{ site.asset_url }}/Bohemian_Eigenvalues/Figure1%402x.png 2x"
         alt="All eigenvalues of 5x5 matrices with entries from \{-1, 0, 1\}"
         width="700"
         height="636">
    <figcaption><b>Figure 1:</b> Density plot in the complex plane of all Bohemian eigenvalues from a random  $5 \times 5$ matrix with entries drawn uniformly from the set $\{-1, 0, 1\}$. This image is viewed on $-3.3-3i$ to $3.3 + 3i$. A high resolution version can be found <a href="{{ site.asset_url }}/Bohemian_Eigenvalues/Figure1_Original.png" target="_blank">here</a>.
    </figcaption>
</figure>

Figure 1 leads to a few obvious questions about this particular Bohemian eigenvalue distribution:

- What is the size of the regions where no eigenvalues appear? We call these *eigenvalue exclusion zones*.
- How does this change if we look at $6 \times 6$ matrices instead?
- Why is there a diffraction pattern appearing around the origin?
- Why are some eigenvalue exclusion zones star shaped?
- Why is the boundary around the distribution of eigenvalues not circular? This may be hard to see but it does flatten out at the top and bottom.

Unfortunately, I am currently unable to answer any of these questions.

## Roots of Polynomials
Before I discuss Bohemian eigenvalues further, there is some closely related work on the distributions of the roots of polynomials. Consider all degree 19 monic polynomials with coefficients in $\\{-1, 1\\}$, these are known as <a href="https://en.wikipedia.org/wiki/Littlewood_polynomial" target="_blank">Littlewood polynomials</a>. $2^{19} = 524,\\!288$ such polynomials exist. <a href="#Figure2" >Figure 2</a> shows a density plot in the complex plane of the set of all roots.

<figure class="align-center" style="width: 700px;">
    <a name="Figure2"></a>
    <img src="{{ site.asset_url }}/Bohemian_Eigenvalues/Figure2.png"    
         srcset="{{ site.asset_url }}/Bohemian_Eigenvalues/Figure2.png 1x, {{ site.asset_url }}/Bohemian_Eigenvalues/Figure2%402x.png 2x"
         alt="All roots of monic degree 19 polynomials with coefficients sampled from \{-1, +1\}."
         width="700"
         height="700">
    <figcaption><b>Figure 2:</b> Density plot in the complex plane of the set of roots of monic degree 19 polynomials with coefficients in $\{-1, 1\}$. This image is viewed on $-1.7-1.7i$ to $1.7 + 1.7i$. A high resolution version can be found <a href="{{ site.asset_url }}/Bohemian_Eigenvalues/Figure2_Original.png">here</a>.
    </figcaption>
</figure>

Exploring the roots of polynomials can be restated as a Bohemian eigenvalue problem. Consider a $19 \times 19$ <a href="https://en.wikipedia.org/wiki/Companion_matrix" target="_blank">Frobenius companion matrix</a> such that the values in the final column (or row depending on your preference) are sampled uniformly from the set $\\{-1, 1\\}$. Clearly, $2^{19}$ such matrices exist, and the set of characteristic polynomials of these matrices is equivalent to the set of monic degree 19 Littlewood polynomials mentioned previously. The Bohemian eigenvalues from this distribution of matrices is equivalent to the distribution of the roots of the degree 19 Littlewood polynomials. Hence, we can restate any problem that explores the roots of polynomials as a Bohemian eigenvalue problem by simply using the companion matrix.

Some of the key people who have studied the roots of such polynomials, and related polynomials include: <a href="http://jdc.math.uwo.ca/" target="_blank">Dan Christensen</a>, <a href="http://math.ucr.edu/home/baez/" target="_blank">John Baez</a>, and <a href="http://www.cecm.sfu.ca/~pborwein/" target="_blank">Peter Borwein</a>. Numerous references include:

- <a href="http://www.jstor.org/stable/2695413" target="_blank">Borwein, P., & Jörgenson, L. (2001). Visible structures in number theory. The American Mathematical Monthly, 108(10), 897-910.</a>
- <a href="http://www.math.ksu.edu/~pinner/Pubs/sep.ps" target="_blank">Borwein, P., & Pinnery, C. (1996). Polynomials with \{0, +1, -1\} Coefficients and a Root Close to a Given Point.</a>
- <a href="http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.47.9327&rep=rep1&type=pdf#page=175" target="_blank">Odlyzko, A. (1992, September). Zeros of polynomials with 0, 1 coefficients. In Algorithms Seminar, 1992-1993 (p. 169).</a>
- <a href="https://arxiv.org/abs/1504.08058" target="_blank">Reyna, R., & Damelin, S. (2015). On the Structure of the Littlewood Polynomials and their Zero Sets. arXiv preprint arXiv:1504.08058.</a>
- <a href="http://mathworld.wolfram.com/PolynomialRoots.html" target="_blank">Weisstein, Eric W. "Polynomial Roots." From MathWorld--A Wolfram Web Resource</a>
- <a href="http://jdc.math.uwo.ca/roots/" target="_blank">http://jdc.math.uwo.ca/roots/</a>
- <a href="https://www.scientificamerican.com/article/math-polynomial-roots/" target="_blank">https://www.scientificamerican.com/article/math-polynomial-roots/</a>
- <a href="https://johncarlosbaez.wordpress.com/2011/12/11/the-beauty-of-roots/" target="_blank">https://johncarlosbaez.wordpress.com/2011/12/11/the-beauty-of-roots/</a>
- <a href="http://math.ucr.edu/home/baez/roots/" target="_blank">http://math.ucr.edu/home/baez/roots/</a>
- <a href="http://www.cecm.sfu.ca/~loki/Projects/Roots/" target="_blank">http://www.cecm.sfu.ca/~loki/Projects/Roots/</a>

## <a name="Computation"></a>Computation

Exploring Bohemian eigenvalues can be difficult as the eigenvalues of a large number of matrices (typically more than 1 million) must be computed before the distributions of these problems begin to converge. Numerous techniques were used in the exploration of the distributions and their properties.

### Make Your Own Bohemian Eigenvalue Plot
Writing a Matlab script to sample random matrices and compute their eigenvalues can be done in a few simple lines. Using the same example as in <a href="#Figure1" >Figure 1</a>, the following Matlab script will compute the eigenvalues of 1 million random $5 \times 5$ matrices where the entries are sampled from $\\{-1, 0, 1\\}$:

{% highlight matlab %}
% A matrix for storing the eigenvalues
eigVals = zeros(1e6, 5);

for i = 1:1e6
    % Generate a random matrix
    A = randsample([-1, 0, 1], 25, true);
    A = reshape(A, [5, 5]);

    % Compute and store the eigenvalues
    eigVals(i, :) = eig(A);
end

% Convert the eigVals matrix to a vector
eigVals = reshape(eigVals, [5e6, 1]);
{% endhighlight %}

The `eigVals` vector now contains 5 million complex values representing eigenvalues from this class. Creating a density plot in the complex plane of these 5 million complex numbers can be done in 2 lines of code thanks to the <a href="https://www.mathworks.com/help/stats/hist3.html" target="_blank">bivariate histogram function</a> (`hist3`) in Matlab.

{% highlight matlab %}
d = hist3([imag(eigVals), real(eigVals)], [1000, 1000]);
imagesc(log(d));
{% endhighlight %}

And just like that, in only 9 lines of Matlab code, you have your very own Bohemian eigenvalue plot (almost as nice as <a href="#Figure1" >Figure 1</a>). Here is an example of what the output will look like:

<figure class="align-center" style="width: 650px;">
    <a name="Figure3"></a>
    <img src="{{ site.asset_url }}/Bohemian_Eigenvalues/Figure3.png"    
         srcset="{{ site.asset_url }}/Bohemian_Eigenvalues/Figure3.png 1x, {{ site.asset_url }}/Bohemian_Eigenvalues/Figure3%402x.png 2x"
         alt="Your very own Bohemian eigenvalue plot."
         width="650"
         height="487">
    <figcaption><b>Figure 3:</b> A density plot in the complex plane of 5 million eigenvalues arising from sampling 1 million $5 \times 5$ matrices.
    </figcaption>
</figure>

If you are feeling adventurous (and you have the <a href="https://www.mathworks.ca/products/parallel-computing/" target="_blank">Parallel Computing Toolbox</a> installed in Matlab), you can change that `for` loop to a `parfor` loop for a nice speedup. Or you could try changing the colormap (try `colormap hot`).

### A General Purpose Matlab Program for Producing Bohemian Eigenvalue Images
If you can produce a Bohemian eigenvalue plot in 9 lines, why does <a href="https://github.com/BohemianMatrices/BHIME-Project" target="_blank">my implementation</a> contain thousands of lines? There are a few reasons:

1. The code I have written has been interfaced such that it is easy use when quickly exploring various classes of matrices.
2. I have optimized the code to be (probably) as fast as you can get pure Matlab code to perform this computation.
3. My implementation automatically produces a readme file with information about the image produced (this can be extremely useful when quickly exploring lots of classes of matrices and forgetting to keep track of what they are).
4. What if you want to explore more eigenvalues than what you can hold in memory? My implementation can take care of that.

Achieving all of that in a user-friendly package is by no means easy.

### When 847 Billion Matrices Is Too Much for Matlab
You may have noticed that <a href="#Figure1" >Figure 1</a> is the result of computing the eigenvalues of 847 billion matrices. This corresponds to 1,839,102 unique characteristic polynomials and 7,963,249 unique eigenvalues. I will discuss how I determined the exact number of unique characteristic polynomials and eigenvalues in a moment.

If you take the small script above, and modify it slightly to ensure all possible matrices are explored, and try to run it, you will very quickly run out of memory. In fact, it would require over 60TB of memory to store all the eigenvalues. Even if you broke the problem into chunks that are small enough to fit into memory, the time needed to finish this computation would be enormous (estimated to be over 3 months on my computer).

### Symmetries
There are of course symmetries within the set of matrices that can be used to reduce the number of matrices one must explore. Some of the obvious symmetries include:

- Both $A$ and $-A$ are in the set of matrices, and the eigenvalues of $A$ are the negative (reflection across the imaginary axis) of the eigenvalues of $-A$.
- $A$ and $A^T$ have the same eigenvalues.
- Any upper or lower triangular matrices have their eigenvalues along the diagonal; the remaining values in the matrix are irrelevant.
- Let $P$ be a permutation matrix. $PAP^{-1}$ will have the same eigenvalues of $A$ and also be in the original set.
- For any class of matrices where the entries are real-valued, the set of eigenvalues will be symmetric across the real axis as a consequence on the <a href="https://en.wikipedia.org/wiki/Complex_conjugate_root_theorem" target="_blank">complex conjugate root theorem</a>. This symmetry could reduce the number of eigenvalues stored by (approximately) half.
- For any class of matrices where the entries are sampled from a symmetric set (if the set contains $x$, it must also contain $-x$), the set of eigenvalues will be symmetric across the imaginary axis. This symmetry could also reduce the number of eigenvalues stored by (approximately) half.

These symmetries, and the many more that likely exist, are interesting in themselves, but provide very little advantage computationally.

### Distributions of Characteristic Polynomials
You may have noticed that I quoted the exact number of characteristic polynomials as 1,839,102, which you may also notice is much less than the 847 billion matrices we started with. In fact, computing the roots of 1.8 million polynomials can be done in less than a minute on (most) modern computers. One other important piece of information is the exact distribution of these characteristic polynomials within the original set of matrices. That is, how many times each characteristic polynomial appears. From this distribution, <a href="#Figure1" >Figure 1</a> can be reproduced exactly!

The next obvious question is how did I find the distribution of characteristic polynomials? The answer is somewhat disappointing. I did an exhaustive computation of the characteristic polynomial for each of the 847 billion matrices, and stored them in a nice data structure. With the power of OpenMP with C++, gcc's maximum optimization level, and a nice (unique) encoding of the characteristic polynomials as unsigned 64-bit integers, I was able to complete this task in under 6 hours on a desktop computer. The result is a 44.5 MB file of all characteristic polynomials and their densities. ~~This file is available on the GitHub repository for the project in the `Data` directory here.~~ The <a href="http://www.bohemianmatrices.com/cpdb/" target="_blank">Characteristic Polynomial Database</a> archives characteristic polynomial files.

### Counting the Eigenvalues
Now that we know the exact distribution of the characteristic polynomials, we can count the exact number of distinct eigenvalues using symbolic computation. Clearly, with 1.8 million characteristic polynomials of degree 5, an upper bound on the number of eigenvalues is about 9 million. The exact number turns out to be a little smaller at 7,963,249. This is the result of some characteristic polynomials sharing roots, and some having multiple roots.

Counting the exact number of eigenvalues can be done as follows:

1. Let $S$ be the set of all characteristic polynomials from the class of matrices for which we want to count all distinct eigenvalues.
2. Let $S_{\\text{sqrfree}}$ be the set of square-free factors from computing the square-free factorization of each element in $S$.
3. Let $G$ be a gcd-free basis of $S_{\\text{sqrfree}}$.
4. The number of distinct roots in $S$ is the sum of the degrees of the polynomials in $G$.

Sounds simple enough, and luckily Maple has an implementation of the <a href="http://www.maplesoft.com/support/help/maple/view.aspx?path=PolynomialTools%2FGcdFreeBasis" target="_blank">gcd-free basis algorithm</a>. In practice, this works well when the number of polynomials in $S$ is small. Unfortunately, 1.8 million polynomials, as in the $5 \\times 5$ matrix example, is not considered small when working in Maple.

In this case I resorted to using modular computation with my own gcd-free basis implementation in Maple. This implementation is by no means optimal; in fact, it could use a lot of improvement, but it was capable of determining (with high probability) that there are 7,963,249 distinct eigenvalues in the class of $5 \\times 5$ matrices with entries sampled from $\\{-1, 0, 1\\}$. This computation did take slightly over a month to run, and was run modulo a number of different prime numbers including one on the order of $10^{18}$.

Symbolic methods are great, except that they are horribly slow. For counting distinct roots we are unfortunately forced to use symbolic methods as the numerical error in the eigenvalues makes it difficult to decide if eigenvalues are actually equal, or only nearly so.

### Some Properties
Consider $n \\times n$ matrices where the entries are drawn uniformly from the set $\\{-1, 0, 1\\}$. Table 1 contains a number of properties of this class of matrices for various values of $n$.

<figure>
<a name="Table1"></a>
<table class="set-table">
    <thead>
        <tr>
            <th>Matrix Size</th>
            <th>Number of Matrices</th>
            <th>Number of Characteristic Polynomials</th>
            <th>Number of Eigenvalues</th>
        </tr>
    </thead>
<tbody>
</tbody>
    <tr>
        <td>OEIS</td>
        <td><a href="http://oeis.org/A060722" target="_blank">A060722</a></td>
        <td><a href="http://oeis.org/A272658" target="_blank">A272658</a></td>
        <td><a href="http://oeis.org/A271570" target="_blank">A271570</a></td>
    </tr>
    <tr>
        <td>$1 \times 1$</td>
        <td>$3^1 = 3$</td>
        <td>$3$</td>
        <td>$3$</td>
    </tr>
    <tr>
        <td>$2 \times 2$</td>
        <td>$3^{2^2} = 81$</td>
        <td>$16$</td>
        <td>$21$</td>
    </tr>
    <tr>
        <td>$3 \times 3$</td>
        <td>$3^{3^2} = 19,\!683$</td>
        <td>$209$</td>
        <td>$375$</td>
    </tr>
    <tr>
        <td>$4 \times 4$</td>
        <td>$3^{4^2} = 43,\!046,\!721$</td>
        <td>$8,\!739$</td>
        <td>$24,\!823$</td>
    </tr>
    <tr>
        <td>$5 \times 5$</td>
        <td>$3^{5^2} = 847,\!288,\!609,\!443$</td>
        <td>$1,\!839,\!102$</td>
        <td>$7,\!963,\!249$</td>
    </tr>
    <tr>
        <td>$6 \times 6$ </td>
        <td>$3^{6^2} = 150,\!094,\!635,\!296,\!999,\!121$</td>
        <td>?</td>
        <td>?</td>
    </tr>
</table>
<figcaption><b>Table 1:</b> Properties of the class of $n \times n$ matrices where the entries are sampled from $\{-1, 0, 1\}$. Links to the <a href="http://oeis.org/" target="_blank">Online Encyclopedia of Integer Sequences (OEIS)</a> are given.
</figcaption>
</figure>

## Some Fun Examples

### Diffraction Patterns
This is another one of my favourite examples (okay...they're all my favourite) that leads to some interesting diffraction patterns in the resulting plot. This example looks at $5 \\times 5$ matrices where the entries are drawn uniformly from the set $\\{-20, -1, 0, 1, 20\\}$. <a href="#Figure4" >Figure 4</a> shows the resulting density plot of the Bohemian eigenvalues in the complex plane.

<figure class="align-center" style="width: 700px;">
    <a name="Figure4"></a>
    <img src="{{ site.asset_url }}/Bohemian_Eigenvalues/Figure4.png"    
         srcset="{{ site.asset_url }}/Bohemian_Eigenvalues/Figure4.png 1x, {{ site.asset_url }}/Bohemian_Eigenvalues/Figure4%402x.png 2x"
         alt="Eigenvalues of a sample of 100 million 5x5 matrices with entries drawn uniformly from the set \{-20, -1, 0, 1, 20\}."
         width="700"
         height="573">
    <figcaption><b>Figure 4:</b> Density plot in the complex plane of the Bohemian eigenvalues from a sample of 100 million random  $5 \times 5$ matrices with entries drawn uniformly from the set $\{-20, -1, 0, 1, 20\}$. This image is viewed on $-55-45i$ to $55 + 45i$. A high resolution version can be found <a href="{{ site.asset_url }}/Bohemian_Eigenvalues/Figure4_Original.png" target="_blank">here</a>.
    </figcaption>
</figure>

### Fibonacci Numbers
This example explores $5 \\times 5$ matrices with entries sampled from the set of Fibonacci numbers up to (and including) 514,229. A sample of 500 million matrices was used. The resulting Bohemian eigenvalue image can be seen in <a href="#Figure5" >Figure 5</a>. One thing you may notice about this plot is the lack of symmetry across the imaginary axis. This is a result of the original set of values that the entries are sampled from not being symmetric. That is, given a matrix $A$, $-A$ is not in the set of possible matrices for this distribution.

<figure class="align-center" style="width: 700px;">
    <a name="Figure5"></a>
    <img src="{{ site.asset_url }}/Bohemian_Eigenvalues/Figure5.png"    
         srcset="{{ site.asset_url }}/Bohemian_Eigenvalues/Figure5.png 1x, {{ site.asset_url }}/Bohemian_Eigenvalues/Figure5%402x.png 2x"
         alt="Eigenvalues of a sample of 500 million 5x5 matrices with entries drawn uniformly from the set of Fibonacci numbers up to and including 514,229"
         width="700"
         height="700">
    <figcaption><b>Figure 5:</b> Density plot in the complex plane of the Bohemian eigenvalues from a sample of 500 million random  $5 \times 5$ matrix with entries drawn uniformly from the set of Fibonacci numbers up to and including 514,229. This image is viewed on $-550,\!000-550,\!000i$ to $550,\!000 + 550,\!000i$. A high resolution version can be found <a href="{{ site.asset_url }}/Bohemian_Eigenvalues/Figure5_Original.png" target="_blank">here</a>.
    </figcaption>
</figure>

### Eigenfish
This is by far one of the most visually interesting classes that I have explored in this project. Here I will only present some of my favourite images from this class of matrices. I have decided to call these particular distributions "eigenfish" as many of the images resemble sea creatures (see <a href="#Figure7" >Figure 7</a>).

As an example, consider a random matrix of the form

$$
    \begin{bmatrix}
		0 & 0 & 0 & U\\
		-1 & -1 & 1 & 0\\
		V & 0 & 0 & 0\\
		-1 & -1 & -1 & -1
	\end{bmatrix}
$$

where $U$ and $V$ are drawn independently from a continuous uniform distribution on $(-5, 5)$. This is of course an infinite set of matrices, yet the eigenvalues from this set are bounded by the region seen in <a href="#Figure6" >Figure 6</a>. This region is the projection of the semi-algebraic set defined by the solutions of $f(\\lambda, U, V) = \\mathrm{det}(A - \\lambda I) = 0$ onto the complex plane in $\\lambda$.

<figure class="align-center" style="width: 650px;">
    <a name="Figure6"></a>
    <img src="{{ site.asset_url }}/Bohemian_Eigenvalues/Figure6.png"    
         srcset="{{ site.asset_url }}/Bohemian_Eigenvalues/Figure6.png 1x, {{ site.asset_url }}/Bohemian_Eigenvalues/Figure6%402x.png 2x"
         alt="Eigenvalues from a sample of 30 million matrices with 2 fixed, uniformly varying parameters."
         width="650"
         height="520">
    <figcaption><b>Figure 6:</b> Plot on the complex plane of the eigenvalues from a sample of 30 million matrices, viewed on $-3-3.7i$ to $3 + 3.7i$. This image is shaded based on the density of eigenvalues at each point (pixel) such that darker regions are of lower density.
    </figcaption>
</figure>

Endless numbers of these images can be produced and <a href="#Figure7" >Figure 7</a> presents 12 such images. Each image is the eigenvalues from the set of matrices where two entries (fixed location in matrix) vary uniformly between -1 and 1.

<figure class="align-center" style="width: 700px;">
    <a name="Figure7"></a>
    <img src="{{ site.asset_url }}/Bohemian_Eigenvalues/Figure7.png"    
         srcset="{{ site.asset_url }}/Bohemian_Eigenvalues/Figure7.png 1x, {{ site.asset_url }}/Bohemian_Eigenvalues/Figure7%402x.png 2x"
         alt="Several plots of the sets of eigenvalues from classes of 3x3 matrices where 2 entries are fixed and vary uniformly."
         width="700"
         height="528">
    <figcaption><b>Figure 7:</b> 12 Bohemian eigenvalue distributions from classes of $3 \times 3$ matrices where 2 entries (fixed) are sampled independently from a continuous uniform distribution on $[-1, 1]$.
    </figcaption>
</figure>

## Acknowledgements
There are a few people who have helped with the progress of this project so far. First, we would like to thank <a href="http://blog.wolfram.com/author/daniel-lichtblau/" target="_blank">Daniel Lichtblau</a> for verifying our findings for the number of unique characteristic polynomials for several classes of matrices, as well as for his suggestions for improving our method of counting characteristic polynomials. We also thank <a href="http://www4.ncsu.edu/~kaltofen/" target="_blank">Erich Kaltofen</a> for pointing out references to Tao and Vu for the asymptotic distributions of related problems. Finally, we would like to thank Sonia Gupta, Jonathan Brino-Tarasoff and Venkat Balasubramanian for their contributions to this project.
