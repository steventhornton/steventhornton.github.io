---
title: "Markov Chains in LaTeX"
categories:
  - Blog
excerpt: "The TikZ package is a great tool for generating publication quality illustrations of Markov chains in LaTeX. This article presents 3 examples along with the complete LaTeX source. At each step, links are provided to the LaTeX source on Overleaf where you can view and edit the examples."
---

The TikZ package is a great tool for generating publication quality illustrations of Markov chains in LaTeX. This article presents 3 examples along with the complete LaTeX source. At each step, links are provided to the LaTeX source on <a href="https://www.overleaf.com/" target="_blank">Overleaf</a> where you can view and edit the examples.

## __Example 1:__ A Simple Weather Model
To busy to read the explanation? The final LaTeX source for this example can be found on <a href="https://www.overleaf.com/read/yrwnwnzstvbf" target="_blank">Overleaf</a>.

The 2-state weather model is often used as a simple introductory model to Markov chains. This model is a discrete-time Markov chain consisting of two states: sunny and rainy. On a given day, the probability of it raining or being sunny the following day is determined by the current days weather. In this example we will create an illustration of this Markov chain.

To get started, we must load the TikZ package:

{% highlight latex %}
\usepackage{tikz}
{% endhighlight %}

For our Markov chain diagrams we will use the `automata` and `positioning` libraries within TikZ, these are loaded using the `\usetikzlibrary`  command:

{% highlight latex %}
\usetikzlibrary{automata, positioning}
{% endhighlight %}

The `tikzpicture` environment is where our diagram will be built:

{% highlight latex %}
\begin{tikzpicture}
    % Drawing goes here
\end{tikzpicture}
{% endhighlight %}

Drawing the Markov chain is broken into two steps:
- draw the states (nodes), and
- draw arrows connecting the states.

### Drawing the States
Within the `tikzpicture` environment, states can be added using the `\node`  command:

{% highlight latex %}
\node[options] (id) {text};
{% endhighlight %}

For each node, you must specify a unique `id`. This id can be any combination of letters and numbers and will be used later to identify the node when connecting the states with arrows. Adding text to a node is optional, although if you chose not to you must use empty curly brackets `{}` . To add the node for the _Sunny_ state to our diagram we use:

{% highlight latex %}
\node[state] (s) {Sunny};
{% endhighlight %}

Here `s` is the label for our node. The `state` option controls the appearance of the node. Next, we will draw the _Rainy_ state:

{% highlight latex %}
\node[state, right=of s] (r) {Rainy};
{% endhighlight %}

The option `right=of s` will place this node to the right of the Sunny node. If we look at our Markov chain now we will see two states next to one another with no arrows connecting them.

<figure class="align-center" style="width: 300px;">
    <a name="Figure1"></a>
    <img src="{{ site.asset_url }}/Markov_Chains_in_LaTeX/SunnyRainy1.png"    
         srcset="{{ site.asset_url }}/Markov_Chains_in_LaTeX/SunnyRainy1.png 1x, {{ site.asset_url }}/Markov_Chains_in_LaTeX/SunnyRainy1%402x.png 2x"
         alt="Sunny and Rainy states."
         width="300"
         height="110">
    <figcaption><b>Figure 1:</b> View LaTeX source on <a href="https://www.overleaf.com/read/xvrsmjkppybt" target="_blank">Overleaf</a>.
    </figcaption>
</figure>

### Connecting the States
The `\draw` command is used to add arrows to our Markov chain representing transitions between states. The `\draw` command takes the form

{% highlight latex %}
\draw[options]
    (startId1) edge[options] node {edge text} (endId1)
    (startId2) edge[options] node {edge text} (endId2)
    ...
    (startIdN) edge[options] node {edge text} (endIdN);
{% endhighlight %}

where the start and end ids reference nodes previously defined. For our chain we will use the option `every loop, bend right` with the `\draw` command. This will draw arrows that bend to the right (from the perspective of the node at the starting end of the arrow) between the nodes. To specify that we want arrows connecting the sunny node to the rainy node and the rainy node to the sunny node we use

{% highlight latex %}
(s) edge node {} (r)
(r) edge node {} (s);
{% endhighlight %}

after the draw command. Note that no options have been used here for either arrow.

Putting everything together, the following LaTeX markup will create an illustration of the Markov chain consisting of 2-states, sunny and rainy, with curved arrows showing the transitions between the states.
rainy node to the sunny node we use

{% highlight latex %}
\documentclass{standalone}

\usepackage{tikz}
\usetikzlibrary{automata,positioning}

\begin{document}
    \begin{tikzpicture}
        % Draw the states
        \node[state]             (s) {Sunny};
        \node[state, right=of s] (r) {Rainy};

        % Connect the states with arrows
        \draw[every loop]
            (s) edge[bend right] node {} (r)
            (r) edge[bend right] node {} (s);
    \end{tikzpicture}
\end{document}
{% endhighlight %}

Now our diagram is starting to look a little better
<figure class="align-center" style="width: 300px;">
    <a name="Figure2"></a>
    <img src="{{ site.asset_url }}/Markov_Chains_in_LaTeX/SunnyRainy2.png"    
         srcset="{{ site.asset_url }}/Markov_Chains_in_LaTeX/SunnyRainy2.png 1x, {{ site.asset_url }}/Markov_Chains_in_LaTeX/SunnyRainy2%402x.png 2x"
         width="300"
         height="110">
    <figcaption><b>Figure 2:</b> Markov chain with arrows to illustrate transitions between states. View LaTeX source on <a href="https://www.overleaf.com/read/tpjzgbgcsgtm" target="_blank">Overleaf</a>.
    </figcaption>
</figure>

### Adding More Arrows
Suppose we wanted to add arrows to indicate that at each time step there is some probability of remaining in the same state. This can be done by adding arrows that connect nodes to themselves. To do this for each state we add

{% highlight latex %}
(s) edge[loop above] node {} (s)
(r) edge[loop above] node {} (r)
{% endhighlight %}

after the `\draw` command. The `loop above` option draws the arrow above the state.
<figure class="align-center" style="width: 300px;">
    <a name="Figure3"></a>
    <img src="{{ site.asset_url }}/Markov_Chains_in_LaTeX/SunnyRainy3.png"    
         srcset="{{ site.asset_url }}/Markov_Chains_in_LaTeX/SunnyRainy3.png 1x, {{ site.asset_url }}/Markov_Chains_in_LaTeX/SunnyRainy3%402x.png 2x"
         width="300"
         height="174">
    <figcaption><b>Figure 3:</b> View LaTeX source on <a href="https://www.overleaf.com/read/ktxdcmrjvfrk" target="_blank">Overleaf</a>.
    </figcaption>
</figure>

### Adding Labels
The next thing we may want to add to our diagram is labels for the transition probabilities between states. This can be done by adding text between the curly brackets for each arrow. You can use inline math for the labels as well if you want to use symbols or expressions. Our states will now take the form:

{% highlight latex %}
(s) edge[bend right] node {0.6} (r)
(r) edge[bend right] node {0.7} (s)
(s) edge[loop above] node {0.4} (s)
(r) edge[loop above] node {0.3} (r);
{% endhighlight %}

which produces the image
<figure class="align-center" style="width: 300px;">
    <a name="Figure4"></a>
    <img src="{{ site.asset_url }}/Markov_Chains_in_LaTeX/SunnyRainy4.png"    
         srcset="{{ site.asset_url }}/Markov_Chains_in_LaTeX/SunnyRainy4.png 1x, {{ site.asset_url }}/Markov_Chains_in_LaTeX/SunnyRainy4%402x.png 2x"
         width="300"
         height="203">
    <figcaption><b>Figure 4:</b> We need to fix the labels. View LaTeX source on  <a href="https://www.overleaf.com/read/qmhvcxbvxkmm" target="_blank">Overleaf</a>.
    </figcaption>
</figure>

Clearly we need to correct the positioning of the labels. To do this we will add an option to the arrows from sunny to rainy and rainy to sunny that controls where the labels are positioned relative to the arrows.

{% highlight latex %}
(s) edge[bend right, auto=left]  node {0.6} (r)
(r) edge[bend right, auto=right] node {0.7} (s)
(s) edge[loop above]             node {0.4} (s)
(r) edge[loop above]             node {0.3} (r);
{% endhighlight %}
Why is one `auto=left` and the other `auto=right`? Every option that we specify is from the point of view of the start of the arrow.

<figure class="align-center" style="width: 300px;">
    <a name="Figure5"></a>
    <img src="{{ site.asset_url }}/Markov_Chains_in_LaTeX/SunnyRainy5.png"    
         srcset="{{ site.asset_url }}/Markov_Chains_in_LaTeX/SunnyRainy5.png 1x, {{ site.asset_url }}/Markov_Chains_in_LaTeX/SunnyRainy5%402x.png 2x"
         width="300"
         height="203">
    <figcaption><b>Figure 5:</b> View LaTeX source on <a href="https://www.overleaf.com/read/twypxsynxftf" target="_blank">Overleaf</a>.
    </figcaption>
</figure>

In its current state, this would probably be appropriate for a research article or report. If you need a nice visual for a slideshow, video or website, you may consider some additional styling.

### Making it Look Amazing
Let’s add some color and adjust the styling of a little bit to make this perfect for presentations. To start, we will change the colors of the nodes. Some additional options are used with the `\node` command. There are ways to define options that can be used for all nodes but I will wait until the next example to get into that.

{% highlight latex %}
\node[state,
      text=yellow,
      draw=none,
      fill=gray!50!black] (s) {Sunny};
\node[state,
      right=of s,
      text=blue!30!white, 
      draw=none, 
      fill=gray!50!black] (r) {Rainy};
{% endhighlight %}

- The `text` option controls the color of the text.
- The `draw` option controls the style of the border. `draw=none` will prevent the border from being drawn.
- The `fill` option is used to set the background color for the node. Here I have used `gray!50!black` which means a mix of 50% gray and 50% black.

The figure is starting to look more colorful

<figure class="align-center" style="width: 300px;">
    <a name="Figure6"></a>
    <img src="{{ site.asset_url }}/Markov_Chains_in_LaTeX/SunnyRainy6.png"    
         srcset="{{ site.asset_url }}/Markov_Chains_in_LaTeX/SunnyRainy6.png 1x, {{ site.asset_url }}/Markov_Chains_in_LaTeX/SunnyRainy6%402x.png 2x"
         width="300"
         height="203">
    <figcaption><b>Figure 6:</b> View LaTeX source on <a href="https://www.overleaf.com/read/qfxxctnjbwtc" target="_blank">Overleaf</a>.
    </figcaption>
</figure>

The nodes can be moved further apart to avoid appearing crowded by changing `right=of s` to `right=2cm of s`. This will place the nodes 2cm way from each other.

Next, the color, width and arrow head of the arrows will be adjusted. This is done using options for the `\draw` command.

{% highlight latex %}
\draw[every loop,
      auto=right,
      line width=1mm,
      >=latex,
      draw=orange,
      fill=orange]
{% endhighlight %}

- `line width` sets the width of the arrows.
- `>=latex` controls the style of the arrow head where latex  is one of the allowed styles (see <a href="https://stuff.mit.edu/afs/athena/contrib/tex-contrib/beamer/pgf-1.01/doc/generic/pgf/version-for-tex4ht/en/pgfmanualse24.html" target="_blank">https://stuff.mit.edu/afs/athena/contrib/tex-contrib/beamer/pgf-1.01/doc/generic/pgf/version-for-tex4ht/en/pgfmanualse24.html</a> for more styles).
- `draw` and `fill` control the color of the arrow.

The font used for the diagram can be changed to a sans-serif font by adding the `font=\sffamily` option to the `\begin{tikzpicture}` command.

{% highlight latex %}
\documentclass{standalone}

\usepackage{tikz}
\usetikzlibrary{automata, positioning}

\begin{document}
    \begin{tikzpicture}[font=\sffamily]

    % Add the states
    \node[state,
          text=yellow,
          draw=none,
          fill=gray!50!black] (s) {Sunny};
    \node[state,
          right=2cm of s,
          text=blue!30!white, 
          draw=none, 
          fill=gray!50!black] (r) {Rainy};

    % Connect the states with arrows
    \draw[every loop,
          auto=right,
          line width=1mm,
          >=latex,
          draw=orange,
          fill=orange]
        (s) edge[bend right, auto=left]  node {0.6} (r)
        (r) edge[bend right, auto=right] node {0.7} (s)
        (s) edge[loop above]             node {0.4} (s)
        (r) edge[loop above]             node {0.3} (r);
   \end{tikzpicture}
\end{document}
{% endhighlight %}

This produces the figure

<figure class="align-center" style="width: 300px;">
    <a name="Figure7"></a>
    <img src="{{ site.asset_url }}/Markov_Chains_in_LaTeX/SunnyRainy7.png"    
         srcset="{{ site.asset_url }}/Markov_Chains_in_LaTeX/SunnyRainy7.png 1x, {{ site.asset_url }}/Markov_Chains_in_LaTeX/SunnyRainy7%402x.png 2x"
         width="300"
         height="170">
    <figcaption><b>Figure 7:</b> View LaTeX source on <a href="https://www.overleaf.com/read/rhhqszhvppcq" target="_blank">Overleaf</a>.
    </figcaption>
</figure>

## __Example 2:__ Bull-Bear-Stagnant Markov Chain

In this example we will be creating a diagram of a three-state Markov chain where all states are connected. We will arrange the nodes in an equilateral triangle. In the previous example, the rainy node was positioned using `right=of s`. In this example we will use absolute position where we specify where we want the node to be. This uses the standard cartesian coordinate system for the positioning of the nodes.

{% highlight latex %}
\documentclass{standalone}

\usepackage{tikz}
\usetikzlibrary{automata, positioning}

\begin{document}
    \begin{tikzpicture}[font=\sffamily]

        % Setup the style for the states
        \tikzset{node style/.style={state, 
                                    minimum width=2cm,
                                    line width=1mm,
                                    fill=gray!20!white}}

        % Draw the states
        \node[node style] at (0, 0)     (bull)     {Bull};
        \node[node style] at (6, 0)     (bear)     {Bear};
        \node[node style] at (3, -5.196) (stagnant) {Stagnant};

        % Connect the states with arrows
        \draw[every loop,
              auto=right,
              line width=1mm,
              >=latex,
              draw=orange,
              fill=orange]
            (bull)     edge[bend right=20]            node {0.1} (stagnant)
            (bull)     edge[bend right=20, auto=left] node {0.2} (bear)
            (bear)     edge[bend right=20]            node {0.3} (bull)
            (bear)     edge[bend right=20, auto=left] node {0.4} (stagnant)
            (stagnant) edge[bend right=20]            node {0.2} (bear)
            (stagnant) edge[bend right=20, auto=left] node {0.6} (bull);
    \end{tikzpicture}
\end{document}
{% endhighlight %}

<figure class="align-center" style="width: 400px;">
    <a name="Figure8"></a>
    <img src="{{ site.asset_url }}/Markov_Chains_in_LaTeX/BullBearStagnant.png"    
         srcset="{{ site.asset_url }}/Markov_Chains_in_LaTeX/BullBearStagnant.png 1x, {{ site.asset_url }}/Markov_Chains_in_LaTeX/BullBearStagnant%402x.png 2x"
         width="400"
         height="366">
    <figcaption><b>Figure 8:</b> View LaTeX source on <a href="https://www.overleaf.com/read/wnfjgzwkyzrn" target="_blank">Overleaf</a>.
    </figcaption>
</figure>

## __Example 3:__ A Phase-Type Mortality Model

This example comes from a mortality model that is based on a multi-state Markov chain. If you are interested in the details you can read the paper this model originates from here: <a href="https://www.tandfonline.com/doi/abs/10.1080/10920277.2007.10597486" target="_blank">Markov Aging Process and Phase-Type Law of Mortality</a>.

{% highlight latex %}
\documentclass{standalone}

\usepackage{tikz}
\usetikzlibrary{automata, positioning}

\begin{document}
    \begin{tikzpicture}
        % Setup the style for the states
        \tikzset{node style/.style={state, 
                                    fill=gray!20!white,
                                    rectangle}}

        \node[node style]               (I)   {I};
        \node[node style, right=of I]   (II)  {II};
        \node[node style, right=of II]  (III) {III};
        \node[node style, right=of III] (IV)  {IV};
        \node[node style, right=of IV]  (5)   {5};
        \node[draw=none,  right=of 5]   (5-n) {$\cdots$};
        \node[node style, right=of 5-n] (n)   {$n$};

        % Draw empty nodes so we can connect them with arrows
        \node[draw=none, above=of I]   (a)    {};
        \node[draw=none, below=of I]   (qI)   {};
        \node[draw=none, below=of II]  (qII)  {};
        \node[draw=none, below=of III] (qIII) {};
        \node[draw=none, below=of IV]  (qIV)  {};
        \node[draw=none, below=of 5]   (q5)   {};
        \node[draw=none, below=of n]   (qn)   {};


    \draw[>=latex,
          auto=left,
          every loop]
         (a)   edge node {}                         (I)
         (I)   edge node {$q_{\mathrm{I}}$}         (qI)
         (II)  edge node {$q_{\mathrm{II}}$}        (qII)
         (III) edge node {$q_{\mathrm{III}}$}       (qIII)
         (IV)  edge node {$q_{\mathrm{IV}}$}        (qIV)
         (5)   edge node {$q_5$}                    (q5)
         (n)   edge node {$q_n$}                    (qn)
         (I)   edge node {$\lambda_{\mathrm{I}}$}   (II)
         (II)  edge node {$\lambda_{\mathrm{II}}$}  (III)
         (III) edge node {$\lambda_{\mathrm{III}}$} (IV)
         (IV)  edge node {$\lambda_{\mathrm{IV}}$}  (5)
         (5)   edge node {$\lambda$}                (5-n)
         (5-n) edge node {$\lambda$}                (n);

    \end{tikzpicture}
\end{document}
{% endhighlight %}

<figure class="align-center" style="width: 600px;">
    <a name="Figure9"></a>
    <img src="{{ site.asset_url }}/Markov_Chains_in_LaTeX/PhaseTypeMortalityModel.png"    
         srcset="{{ site.asset_url }}/Markov_Chains_in_LaTeX/PhaseTypeMortalityModel.png 1x, {{ site.asset_url }}/Markov_Chains_in_LaTeX/PhaseTypeMortalityModel%402x.png 2x"
         alt="A multi-state Markov chain."
         width="600"
         height="143">
    <figcaption><b>Figure 9:</b> View LaTeX source on <a href="https://www.overleaf.com/read/ccxcydmtsbhf" target="_blank">Overleaf</a>.
    </figcaption>
</figure>

## References:

- <a href="https://tex.stackexchange.com/questions/89662/how-to-create-a-markov-chain-with-an-empty-node" target="_blank">tex.stackexchange.com/questions/89662/how-to-create-a-markov-chain-with-an-empty-node</a>
- <a href="https://tex.stackexchange.com/questions/20784/which-package-can-be-used-to-draw-automata" target="_blank">tex.stackexchange.com/questions/20784/which-package-can-be-used-to-draw-automata</a>
- X Sheldon Lin and Xiaoming Liu. Markov aging process and phase-type law of mortality. North American Actuarial Journal, 11(4):92–109, 2007.
