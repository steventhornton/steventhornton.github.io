---
title: "Publication Quality Plots with Matlab"
categories:
  - Blog
excerpt: "Matlab provides a powerful plotting interface. Unfortunately, the default settings do not produce the most visually appealing figures. Unlike other popular plotting libraries (ggplot2, matplotlib, highcharts, Plotly, etc.), Matlab has no simple method for changing the overall appearance of a plot. One option is to use a platform such as Plotly within Matlab. In this article I discuss how you can improve the appearance of the native plotting architecture within Matlab to give your figures a more professional, refined look."
---

{: .notice--warning}
Note: This post may be out of date and some code may no longer do what is was originally intended. Since completing my PhD, I no longer have access to Matlab and am unable to update the post.

Matlab provides a powerful plotting interface. Unfortunately, the default settings do not produce the most visually appealing figures. Unlike other popular plotting libraries (<a href="https://ggplot2.tidyverse.org/" target="_blank">ggplot2</a>, <a href="http://matplotlib.org/" target="_blank">matplotlib</a>, <a href="http://www.highcharts.com/" target="_blank">highcharts</a>, <a href="https://plot.ly/" target="_blank">Plotly</a>, etc.), Matlab has no simple method for changing the overall appearance of a plot. One option is to use a platform such as Plotly within Matlab  (more details <a href="https://plot.ly/matlab/" target="_blank">here</a>). In this article I discuss how you can improve the appearance of the native plotting architecture within Matlab to give your figures a more professional, refined look.

As a motivational example, both plots in <a href="#Figure1" >Figure 1</a> were created with Matlab. The plot on the left uses the default plotting parameters, whereas the plot on the right has been adjusted as will be discussed in <a href="#Example1" >Example 1</a>.

<figure class="align-center" style="width: 700px;">
	<a name="Figure1"></a>
  <img src="{{ site.asset_url }}/Publication_Quality_Plots_with_Matlab/Figure1.png"    
	   srcset="{{ site.asset_url }}/Publication_Quality_Plots_with_Matlab/Figure1.png 1x, {{ site.asset_url }}/Publication_Quality_Plots_with_Matlab/Figure1%402x.png 2x" 
	   alt="Example 1" 
	   width="700" 
	   height="216">
  <figcaption><b>Figure 1:</b> Two Matlab plots of the functions $e^{-x/15}\!\sin(x)$ and $e^{-x/15}\!\cos(x)$. Which do you prefer? The left plot uses the default plotting properties in Matlab and the one on the right has been adjusted for a more professional look.
  </figcaption>
</figure>

## Interactive Customization

Before I jump into how you can improve the appearance of a plot within a Matlab script, there is one quick and easy alternative. The Matlab <a href="https://www.mathworks.com/help/matlab/creating_plots/plotting-tools--interactive-plotting.html" target="_blank">plot tools</a> interface can be accessed by the button at the far right of the toolbar in a figure window (see <a href="#Figure2" >Figure 2</a>). This will open a window that allows quick interactive customization of a figure (see <a href="#Figure3" >Figure 3</a>). One feature that makes this interface especially powerful is the ability to export the adjustments you have made to a script. Under File > Generate Code... you can export a Matlab script with the exact settings needed to reproduce the changes you make to your figure within the plot tools interface.

Although the plot tools interface works well, my personal preferences is to keep everything contained within a Matlab script such that when I return to a project weeks or months later, my plotting setup has not changed.

<figure class="align-center" style="width: 713px;">
	<a name="Figure2"></a>
  <img src="{{ site.asset_url }}/Publication_Quality_Plots_with_Matlab/Figure2.png"    
	   srcset="{{ site.asset_url }}/Publication_Quality_Plots_with_Matlab/Figure2.png 1x, {{ site.asset_url }}/Publication_Quality_Plots_with_Matlab/Figure2%402x.png 2x" 
	   alt="Button to open the plot properties GUI" 
	   width="713" 
	   height="426">
  <figcaption><b>Figure 2:</b> The circled button will open Matlab's plot tools interface where you can easily adjust the appearance of a figure. See <a href="#Example2" >Example 2</a> for the Matlab script used to create this figure.
  </figcaption>
</figure>

<figure class="align-center" style="width: 713px;">
	<a name="Figure3"></a>
	<img src="{{ site.asset_url }}/Publication_Quality_Plots_with_Matlab/Figure3.png"    
		 srcset="{{ site.asset_url }}/Publication_Quality_Plots_with_Matlab/Figure3.png 1x, {{ site.asset_url }}/Publication_Quality_Plots_with_Matlab/Figure3%402x.png 2x" 
		 alt="The plot tools interface" 
		 width="713" 
		 height="299">
	<figcaption><b>Figure 3:</b> The plot tools interface in Matlab is useful for quickly adjusting the appearance of a figure.</figcaption>
</figure>

## <a name="Example1"></a>Example 1

I begin with an in-depth example of how to transform the plot in the left panel of <a href="#Figure1" >Figure 1</a> into that in the right panel.

The properties in Matlab that affect the appearance of a figure are broken into two categories: __axes__ properties and __figure__ properties. Within Matlab, the `gca` and `gcf` commands are used to adjust axes and figure properties respectively. Axes properties affect the appearance of elements such as the title, axis labels, tick marks, grid lines etc. A full list of the properties that can be adjusted can be found in the Matlab <a href="https://www.mathworks.com/help/matlab/ref/matlab.graphics.axis.axes-properties.html" target="_blank">axes properties documentation</a>. Figure properties control the appearance of the window. This is where you adjust settings such as the background color, figure window size, position of the plot, etc. Again, the Matlab <a href="https://www.mathworks.com/help/matlab/ref/matlab.ui.figure-properties.html" target="_blank">figure properties documentation</a>[] provides a full list of the properties that can be adjusted.

### Adjusting the Plot Properties

The first step in reproducing the plot in the right panel of <a href="#Figure1" >Figure 1</a> is to create our base plot. This is a plot of the functions $e^{-x/15}\\!\sin(x)$ and $e^{-x/15}\\!\cos(x)$ for $x \\in [0, 12\\pi]$ with no changes to the appearance settings.

{% highlight matlab %}
x = linspace(0, 12*pi, 2016);

y1 = exp(-x/15).*sin(x);
y2 = exp(-x/15).*cos(x);

plot(x, y1, x, y2);
title('A Plot of exp(-x/15)*sin(x) and exp(-x/15)*cos(x)');
{% endhighlight %}

At this point our plot will look the same as the left panel of <a href="#Figure1" >Figure 1</a>. Before adjusting the axes and figure properties, we will work on the appearance of each function changing the line-width and color properties. To keep our code clean, it is best to separate the plotting of each function into different calls to the `plot` function, using the `hold on` command so both functions appear on the same figure.

{% highlight matlab %}
hold on;
plot(x, y1);
plot(x, y2);
{% endhighlight %}

We are now able to adjust the line-width and colors of each curve. There are three ways this can be done. The first applies the properties directly within the `plot` function. The second and third store each plot in a variable, and adjust the properties of that plot variable.

{% highlight matlab %}
% Method 1
hold on;
plot(x, y1, 'LineWidth', 3, 'Color', [124, 188, 37]/255);
plot(x, y2, 'LineWidth', 3, 'Color', [157, 88, 176]/255);

% Method 2
hold on;
p1 = plot(x, y1);
p2 = plot(x, y2);

% Set the properties
set(p1, 'LineWidth', 3, ...
        'Color', [124, 188, 37]/255);
set(p2, 'LineWidth', 3, ...
        'Color', [157, 88, 176]/255);

% Method 3
hold on;
p1 = plot(x, y1);
p2 = plot(x, y2);

% Set the properties
p1.LineWidth = 3;
p2.LineWidth = 3;
p1.Color = [124, 188, 37]/255;
p2.Color = [157, 88, 176]/255;
{% endhighlight %}

All three methods produce the same results, the second and third can be easier to manage if you are adjusting a lot of properties for each plot. A full list of the properties for lines (the functions) can be found in Matlab's documentation  <a href="https://www.mathworks.com/help/matlab/ref/plot.html#inputarg_LineSpec" target="_blank">here</a>. In the remainder of this article I will be using the third method to adjust settings.

One thing I always like to adjust is the colors of lines or points in a figure. Using the `Color` property and a list of 3 rgb values between 0 and 1, you can adjust the color of each line or set of points individually. If you have difficulty deciding on colors that work well together, there are some great tools out there with pre-defined color schemes: <a href="https://color.adobe.com/explore/most-popular/?time=all" target="_blank">https://color.adobe.com/explore/most-popular/?time=all</a>. <a href="#Figure4" >Figure 4</a> shows what the plot looks like after adjusting the line-widths and colors.

<figure class="align-center" style="width: 450px;">
<a name="Figure4"></a>
  <img src="{{ site.asset_url }}/Publication_Quality_Plots_with_Matlab/Figure4.png"    
	   srcset="{{ site.asset_url }}/Publication_Quality_Plots_with_Matlab/Figure4.png 1x, {{ site.asset_url }}/Publication_Quality_Plots_with_Matlab/Figure4%402x.png 2x" 
	   alt="Figure 4" 
	   width="450" 
	   height="278">
  <figcaption><b>Figure 4:</b> After adjusting the line-widths and colors.
  </figcaption>
</figure>

### Axes Properties
The axes properties are where most of the styling happens. The `gca` variable allows control of the axes properties. Unfortunately, any properties we adjust this way will only apply to the current figure. To set global properties, an under-documented feature in Matlab must be used. For more details, see my <a href="{{ site.url }}/blog/default-plot-settings-in-matlab/" >Default Plot Settings in Matlab</a> post.

The first thing we will adjust is the limits of our plot. The `axes` function in Matlab can be used to do this but, as an alternative, you can set the limits using the `XLim` and `YLim` properties. We will need access to the `gca` variable, this can be done by assigning it to a variable `ax = gca;` and working with the `ax` variable. As with plot properties, we can set the axes properties by either using the `set` function such as `set(ax, 'XLim', [0, 12*pi]);` or by directly changing the `ax` variable as `ax.XLim = [0, 12*pi];`, I use the latter. At this point our Matlab script for plotting our functions looks like this:

{% highlight matlab %}
% Generate values from our functions
x = linspace(0, 12*pi, 2016);
y1 = exp(-x/15).*sin(x);
y2 = exp(-x/15).*cos(x);

% Plot the functions
hold on;
p1 = plot(x, y1);
p2 = plot(x, y2);

% Set the plot properties
p1.LineWidth = 3;
p2.LineWidth = 3;
p1.Color = [124, 188, 37]/255;
p2.Color = [157, 88, 176]/255;

% Set the axes properties
ax = gca;
ax.XLim = [0, 12*pi];
ax.YLim = [-1, 1];

title('A Plot of exp(-x/15)*sin(x) and exp(-x/15)*cos(x)');
{% endhighlight %}

The next thing we may want to adjust is the appearance of the ticks along the $x$ and $y$ axes. Since we are plotting trigonometric functions, we will label the $x$-axis in intervals of $2\\pi$.

{% highlight matlab %}
ax.XTick = linspace(0, 12*pi, 7);    % The locations of the tick marks
ax.XTickLabels = {'0', '$2\pi$', '$4\pi$', '$6\pi$', '$8\pi$', '$10\pi$', '$12\pi$'};
ax.TickLabelInterpreter ='LaTeX';
ax.YTick = [-1, 0, 1];
{% endhighlight %}


Notice that a property called `TickLabelInterpreter` has been set to `LaTeX`. This will ensure that the tick labels are rendered using LaTeX so we get nice looking $\\pi$ symbols. To render all the text (titles, axes labels, axes tick labels, etc.) within a plot with LaTeX a few properties must be set.

{% highlight matlab %}
ax.FontName = 'LaTeX';
ax.Title.Interpreter = 'LaTeX';
ax.XLabel.Interpreter = 'LaTeX';
ax.YLabel.Interpreter = 'LaTeX';
{% endhighlight %}

Since the title is now being rendered using LaTeX, inline equations can be included by surrounding them with dollar symbols.

{% highlight matlab %}
% This should appear after defining the axes properties
% otherwise you will see a warning.
title('A Plot of $e^{-x/15}\!\sin(x)$ and $e^{-x/15}\!\cos(x)$');
{% endhighlight %}

The result of adjusting the axes properties can be seen in <a href="#Figure5" >Figure 5</a>, which is nearly identical to the right panel of <a href="#Figure1" >Figure 1</a>.

<figure class="align-center" style="width: 450px;">
<a name="Figure5"></a>
  <img src="{{ site.asset_url }}/Publication_Quality_Plots_with_Matlab/Figure5.png"    
	   srcset="{{ site.asset_url }}/Publication_Quality_Plots_with_Matlab/Figure5.png 1x, {{ site.asset_url }}/Publication_Quality_Plots_with_Matlab/Figure5%402x.png 2x" 
	   alt="Figure 5" 
	   width="450" 
	   height="278">
  <figcaption><b>Figure 5:</b> After adjusting the tick properties, and using LaTeX to render all labels.
  </figcaption>
</figure>

There are a few more axes properties that I like to adjust. First, I prefer not to have the plot surrounded with a box, this can be turned off using `ax.Box = 'off'`. Next, the width of the axis lines can be increased to make them more visible using `ax.LineWidth = 1.5`.

### Figure Properties
The figure properties can be extremely powerful especially if you want to remain consistent in your styling, or want control over the font size of a plot. This can be especially useful if you are including your plot in a LaTeX document and want the font-size of the labels and title to match the font-size used in your LaTeX document. These properties will not affect the appearance of the plot itself, but will allow control over the size of the plotting window so you can be comfortable knowing all your plots are the same dimension.

We will begin by adjusting the paper size for our plots. To gain access to the figure properties we will assign `figure()` to a variable. Here I have set the size of the plot to $5 \\times 3.09$ inches (I used 3.09 so the aspect ratio of the plot is the <a href="https://en.wikipedia.org/wiki/Golden_ratio" target="_blank">golden ratio</a>). The `FigPosition` property will add margins around the plot so the title and axis labels do not get cut off.

`InvertHardcopy` controls the appearance of the plot when you save it. By setting this to `'off'`, Matlab will not make any changes to the appearance of the saved file.

{% highlight matlab %}
fig = figure(1);
fig.PaperPositionMode = 'manual';
fig.PaperUnits = 'inches';
fig.Units = 'inches';
fig.PaperPosition = [0, 0, 5, 3.09];
fig.PaperSize = [5, 3.09];
fig.Position = [0.1, 0.1, 4.9, 2.99];
fig.Resize = 'off';
fig.InvertHardcopy = 'off';

% Previous code here...
{% endhighlight %}

### Putting It All Together
Here is the final script used for creating the plot in the right panel of <a href="#Figure1" >Figure 1</a>:

{% highlight matlab %}
% Generate values from our functions
x = linspace(0, 12*pi, 2016);
y1 = exp(-x/15).*sin(x);
y2 = exp(-x/15).*cos(x);

% Set up the figure properties
fig = figure(1);
fig.PaperPositionMode = 'manual';
fig.PaperUnits = 'inches';
fig.Units = 'inches';
fig.PaperPosition = [0, 0, 5, 3.09];
fig.PaperSize = [5, 3.09];
fig.Position = [0.1, 0.1, 4.9, 2.99];
fig.Resize = 'off';
fig.InvertHardcopy = 'off';

% Set up the axes properties
ax = gca;
ax.XLim = [0, 12*pi];
ax.YLim = [-1, 1];
ax.XTick = linspace(0, 12*pi, 7);    % The locations of the tick marks
ax.XTickLabels = {'0', '$2\pi$', '$4\pi$', '$6\pi$', '$8\pi$', '$10\pi$', '$12\pi$'};
ax.TickLabelInterpreter = 'LaTeX';
ax.YTick = [-1, 0, 1];
ax.FontName = 'LaTeX';
ax.Title.Interpreter = 'LaTeX';
ax.XLabel.Interpreter = 'LaTeX';
ax.YLabel.Interpreter = 'LaTeX';
ax.Box = 'off';
ax.LineWidth = 1.5;
ax.FontSize = 12;

% Plot the functions
hold on;
p1 = plot(x, y1);
p2 = plot(x, y2);

% Set the plot properties
p1.LineWidth = 3;
p2.LineWidth = 3;
p1.Color = [124, 188, 37]/255;
p2.Color = [157, 88, 176]/255;

% Add a title
title('A Plot of $e^{-x/15}\!\sin(x)$ and $e^{-x/15}\!\cos(x)$');
{% endhighlight %}

## <a name="Example2"></a>Example 2

The following Matlab script was used to produce <a href="#Figure2" >Figure 2</a>.

{% highlight matlab %}
% The colors used for each curve
colors = [8, 126, 214;
          10, 113, 196;
          12, 101, 167;
          14, 88, 143;
          16, 76, 120;
          19, 63, 96;
          21, 50, 72;
          23, 38, 49]/255;

% Set the figure properties
fig = figure(1);
fig.Resize = 'off';
fig.PaperUnits = 'inches';
fig.Units = 'inches';
fig.PaperPositionMode = 'manual';
fig.PaperPosition = [0, 0, 10, 5];
fig.PaperSize = [10, 5];
fig.Position = [0.1, 0.1, 9.9, 4.9];
fig.Color = [25, 25, 25]/255;
fig.InvertHardcopy = 'off';

% Set the axes properties
ax = gca;
ax.FontName = 'LaTeX';
ax.FontSize = 16;
ax.TickLabelInterpreter = 'LaTeX';
ax.XLim = [0, 12*pi];
ax.YLim = [-1, 1];
ax.XTick = linspace(0, 12*pi, 13);
ax.XTickLabel = {0, '$\pi$', '$' + string(2:12) + '\pi$'};
ax.YTick = [-1, 0, 1];
ax.XColor = [1, 1, 1];
ax.YColor = [1, 1, 1];
ax.Box = 'off';
ax.Layer = 'top';
ax.Color = [25, 25, 25]/255;

hold on;

for i=0:13
    for j=1:8
        x = linspace((i-1)*pi+1e-10 - (j-1)*pi/5, i*pi-1e-10 - (j-1)*pi/5, 1000);
        l = -exp(-x/15);
        y = l./sin(x + (j-1)*pi/5);

        p = plot(x, y);
        p.LineWidth = 2;
        p.Color = [34, 34, 34]/255;

        p = plot(x, -y);
        p.LineWidth = 2;
        p.Color = [34, 34, 34]/255;
    end
end

x = linspace(0, 12*pi, 1000);
l = exp(-x/15);

for i=8:-1:1
    y = l.*cos(x - (i-1)*pi/5);

    p = plot(x, y);
    p.Color = colors(i,:);
    p.LineWidth = 2;
end
{% endhighlight %}

## Example 3
<figure class="align-center" style="width: 650px;">
	<a name="Figure6"></a>
  <img src="{{ site.asset_url }}/Publication_Quality_Plots_with_Matlab/Figure6.png"    
	   srcset="{{ site.asset_url }}/Publication_Quality_Plots_with_Matlab/Figure6.png 1x, {{ site.asset_url }}/Publication_Quality_Plots_with_Matlab/Figure6%402x.png 2x" 
	   alt="Figure 6" 
	   width="650" 
	   height="325">
  <figcaption><b>Figure 6:</b> US flu infection rates for October 2005 - October 2006.
  </figcaption>
</figure>

The following Matlab script was used to produce <a href="#Figure6" >Figure 6</a>. The `flu` dataset is one of several <a href="https://www.mathworks.com/help/stats/sample-data-sets.html" target="_blank">sample datasets</a> in Matlab. It contains the flu infection rates in several regions of the US between Oct. 2005 and Oct. 2006.

Some notes about the code below:
- `ax.ColorOrder = colors;` sets the default colors to use for each line that is plotted
- `ax.XColor = 'white';` and `ax.YColor = 'white';` controls the color of the $x$ and $y$ axes. Here I used `'white'` as the color instead of a vector of rgb values. `white` is one of the built-in <a href="https://www.mathworks.com/help/matlab/ref/colormap.html" target="_blank">colormaps</a> in Matlab.
- In Matlab everything is a matrix, this means you can create a matrix (a $6 \\times 1$ matrix in this case) of line plots. This can help simplify your scripts when you have several plots and you wish to change the properties (such as `linewidth`) of all of them at once. Note that I used the `set` function here. This doesn't work by directly accessing the plot variable.
- A legend has been added to this plot, and like the properties explored earlier, there are a number of properties specific to a legend that can be modified. A full list can be found <a href="https://www.mathworks.com/help/matlab/ref/legend-properties.html" target="_blank">here</a>[]().
- Just as adding a legend, a title has several properties that affect its appearance, see <a href="https://www.mathworks.com/help/matlab/ref/title.html" target="_blank">Matlab's documentation</a> for more details.

{% highlight matlab %}
% Load the data
load flu.mat

% Convert the Date column to datetime
flu.Date = datetime(flu.Date, 'InputFormat', 'M/dd/yyyy');

% Colors for the lines that will be plotted
colors = [241, 90, 90;
          240, 196, 25;
          78, 186, 111;
          45, 149, 191;
          149, 91, 165]/255;

% Set the figure properties
fig = figure(1);

% Set the paper size
fig.Resize = 'off';
fig.PaperUnits = 'inches';
fig.Units = 'inches';
fig.PaperPositionMode = 'manual';
fig.PaperPosition = [0, 0, 10, 5];
fig.PaperSize = [10, 5];
fig.Position = [0.1, 0.1, 9.9, 4.9];

% Background color
fig.Color = [25, 25, 25]/255;

% Prevent the background color from chaning on save
fig.InvertHardcopy = 'off';


% Set the axes properties
ax = gca;
ax.FontName = 'LaTeX';
ax.TickLabelInterpreter = 'LaTeX';
ax.FontSize = 16;
ax.YLim = [0, 3.5];
ax.YTick = 0:3;
ax.YTickLabel = {string(ax.YTick) + '\%'};
ax.ColorOrder = colors;
ax.XColor = 'white';
ax.YColor = 'white';
ax.Box = 'off';
ax.LineWidth = 1.5;
ax.Color = [25, 25, 25]/255;
ax.YGrid = 'on';

% Plot the data
hold on;
p = [plot(flu.Date, flu.NE);
     plot(flu.Date, flu.MidAtl);
     plot(flu.Date, flu.ENCentral);
     plot(flu.Date, flu.WNCentral);
     plot(flu.Date, flu.SAtl)];

% Set all the line widths at once
set(p, 'LineWidth', 2);

% Set the tick marks for the x-axis to months
datetick('x','mmm', 'keeplimits');

% Add a legend
lgd = legend('North East', ...
             'Mid Atlantic', ...
             'NE Central', ...
             'NW Central', ...
             'South Atlantic');
lgd.Box = 'off';
lgd.Interpreter = 'LaTeX';
lgd.TextColor = 'white';


t = title('US Flu Infection Rates from Oct. 2005 -- Oct 2006');
t.Color = 'white';
t.Interpreter = 'LaTeX';
{% endhighlight %}

## Final Thoughts
The adjustments I have described here are useful when you plan on presenting a figure, whether it is in a paper, on a poster or in a presentation. Hopefully in the future Matlab will introduce an easy way to quickly adjust the appearance of a figure (or maybe I will take this on as a future project).

### References
A few articles have come out covering some ways to improve the appearance of a graph:
- <a href="http://blogs.mathworks.com/loren/2007/12/11/making-pretty-graphs/" target="_blank">http://blogs.mathworks.com/loren/2007/12/11/making-pretty-graphs/</a>
- <a href="https://dgleich.wordpress.com/2013/06/04/creating-high-quality-graphics-in-matlab-for-papers-and-presentations/" target="_blank">https://dgleich.wordpress.com/2013/06/04/creating-high-quality-graphics-in-matlab-for-papers-and-presentations/</a>
- <a href="http://www.matlabtips.com/beautiful-plots-in-matlab/" target="_blank">http://www.matlabtips.com/beautiful-plots-in-matlab/</a>
