---
title: "Default Plot Settings in Matlab"
categories:
  - Blog
excerpt: "Have you ever applied custom styling to a figure in Matlab only to discover this styling does not transfer to subsequent figures? Unfortunately, plot styling only applies to the figure it was initially applied to. Luckily, an under-documented feature in Matlab allows you to set global properties for your figures."
---

{: .notice--warning}
__Note:__ This post may be out of date and some code may no longer do what was originally intended. Since completing my PhD, I no longer have access to Matlab and am unable to verify the accuracy of the post

Have you ever applied custom styling to a figure in Matlab only to discover this styling does not transfer to subsequent figures? Unfortunately, plot styling only applies to the figure it was initially applied to. Luckily, an under-documented feature in Matlab allows you to set global properties for your figures. Matlab features a short document (find it <a href="https://www.mathworks.com/help/matlab/creating_plots/default-property-values.html" target="_blank">here</a>) about setting default properties.

For more details about how to customize the appearance of a plot, see my post <a href="{{ site.url }}/blog/publication-quality-plots-with-matlab/" >Publication Quality Plots with Matlab</a>.

## Accessing the Global Plot Settings
If you run `get(groot, 'Factory')` in the Matlab (`groot` is the <a href="https://www.mathworks.com/help/matlab/ref/groot.html" target="_blank">graphics root object</a>), a list of over 1000 properties and their factory default values will print. These are the factory default properties that Matlab will apply to all plots. These settings cannot be directly changed, but we can override them using with the `deafult` settings.

The default settings can be viewed by running `get(groot, 'Default')`. This may print nothing initially, or it may print a few properties (on my computer it prints `defaultFigurePosition` and `defaultFigurePaperPositionMode`). To set one of these properties, we use `set(groot, propertyName, value)`, where `propertyName` is one of the properties in the list of factory defaults with `factory` replaced by `Default`.

As a simple example, let's say we wanted the default line width to be 10. We can view the factory default line width with `get(groot, 'FactoryLineLineWidth')` which should return `0.5`. To change the default, we use `set(groot, 'DefaultLineLineWidth', 10)`. If you now run `get(groot, 'Default')`, you will see the `DefaultLineLineWidth` property appear with a value of 10. To see this in action, <a href="#Figure1" >Figure 1</a> shows the result of the code snippet below, both figures use the new line width of 10.

<figure class="align-center" style="width: 75%;">
    <a name="Figure1"></a>
    <img src="{{ site.asset_url }}/Default_Plot_Settings_in_Matlab/Figure1.png"    
         srcset="{{ site.asset_url }}/Default_Plot_Settings_in_Matlab/Figure1.png 1x, {{ site.asset_url }}/Default_Plot_Settings_in_Matlab/Figure1%402x.png 2x" 
         alt="Figure 1">
    <figcaption><b>Figure 1:</b> The default line-width of 10 is applied to both figures.
    </figcaption>
</figure>

{% highlight matlab %}
set(groot, 'DefaultLineLineWidth', 10);

x = linspace(0, 2*pi, 1000);

figure(1);
plot(x, sin(x), x, cos(x));

figure(2);
plot(x, besselj(0, x), x, besselj(1, x));
{% endhighlight %}

## Example 1
In this example I will work through how you can create a nice theme for your plots that uses a dark background with brightly colored lines. LaTeX is used to render all fonts in the plot giving it a much more professional look than the standard font used by Matlab.

To start, we will set up our base plot (with the factory default settings applied). Here I will create two figures, one of the <a href="https://en.wikipedia.org/wiki/Bessel_function" target="_blank">Bessel function</a> of the first kind for $\nu = 0\dots 4$, and the second a plot of the <a href="https://en.wikipedia.org/wiki/Legendre_polynomials" target="_blank">Legendre polynomials</a> for $n = 1 \dots 5$. The resulting plots can be seen in <a href="#Figure2" >Figure 2</a> and was produced with the code snippet below the figure.

<figure class="align-center" style="width: 75%;">
    <a name="Figure2"></a>
    <img src="{{ site.asset_url }}/Default_Plot_Settings_in_Matlab/Figure2.png"    
         srcset="{{ site.asset_url }}/Default_Plot_Settings_in_Matlab/Figure2.png 1x, {{ site.asset_url }}/Default_Plot_Settings_in_Matlab/Figure2%402x.png 2x" 
         alt="Figure 2" 
         width="700" 
         height="216">
    <figcaption><b>Figure 2:</b> <i>Left:</i> Plot of the first five Bessel functions of the first kind. <i>Right:</i> Plot of the first five Legendre polynomials. No custom styling has been applied yet.
    </figcaption>
</figure>

{% highlight matlab %}
% Figure 1
figure(1);

x = linspace(0, 20, 1000);

hold on;
for i = 0:4
    plot(x, besselj(i, x));
end
title('Plot of $J_{\nu}(x)$ for $\nu = 0\dots 4$');

% Figure 2
figure(2);

x = linspace(-1, 1, 1000);

hold on;
for i=1:5
    plot(x, legendreP(i, x));
end
title('Plot of $P_i(x)$ for $i=1 \dots 5$');
{% endhighlight %}

Clearly these plots need some work to make them nicer looking. To start, we will change the background color. Two properties need to be changed for this: `DefaultFigureColor` and `DefaultAxesColor`. `FigureColor` controls the background of the figure window, and `AxesColor` controls the background color of the plot area. We will also turn off the property `FigureInvertHardcopy` so Matlab does not change these settings when we save our plot.

{% highlight matlab %}
set(groot, 'DefaultAxesColor', [25, 25, 25]/255);
set(groot, 'DefaultFigureColor', [25, 25, 25]/255);
set(groot, 'DefaultFigureInvertHardcopy', 'off');
{% endhighlight %}

Next, we can change the default colors Matlab uses for the lines. The `AxesColorOrder` property expects an $n \times 3$ matrix of values between 0 and 1 where each row represents an rgb triple. While we are working on the lines, we will set their default width to 3 using `DefaultLineLineWidth`.

{% highlight matlab %}
colors = [241, 90, 90;
          240, 196, 25;
          78, 186, 111;
          45, 149, 191;
          149, 91, 165]/255;
set(groot, 'DefaultAxesColorOrder', colors);
set(groot, 'DefaultLineLineWidth', 3);
{% endhighlight %}

Because we changed the background color to a dark gray, we will need to change the color of all the text to white so it is visible. The colors for the axes lines will also need to be inverted.

{% highlight matlab %}
set(groot, 'DefaultTextColor', [1, 1, 1]);
set(groot, 'DefaultAxesXColor', [1, 1, 1]);
set(groot, 'DefaultAxesYColor', [1, 1, 1]);
{% endhighlight %}

Finally, we can make Matlab use LaTeX to render all text. This will give the figure a much more professional feeling.

{% highlight matlab %}
set(groot, 'DefaultTextInterpreter', 'LaTeX');
set(groot, 'DefaultAxesTickLabelInterpreter', 'LaTeX');
set(groot, 'DefaultAxesFontName', 'LaTeX');
set(groot, 'DefaultLegendInterpreter', 'LaTeX');
{% endhighlight %}

The result of making these changes can be seen in <a href="#Figure3" >Figure 3</a>. Clearly this is a much more attractive plot than in Figure 2, and these settings will be applied to all subsequent figures. The full Matlab code can be found below Figure 3.

<figure class="align-center" style="width: 75%;">
    <a name="Figure3"></a>
    <img src="{{ site.asset_url }}/Default_Plot_Settings_in_Matlab/Figure3.png"    
         srcset="{{ site.asset_url }}/Default_Plot_Settings_in_Matlab/Figure3.png 1x, {{ site.asset_url }}/Default_Plot_Settings_in_Matlab/Figure3%402x.png 2x" 
         alt="Figure 3" 
         width="708" 
         height="216">
    <figcaption><b>Figure 3:</b> <i>Left:</i> Plot of the first five Bessel functions of the first kind. <i>Right:</i> Plot of the first five Legendre polynomials. The factory default style has been overridden to give the plots a more professional look.
    </figcaption>
</figure>

{% highlight matlab %}
% Set the default plot properties
colors = [241, 90, 90;
          240, 196, 25;
          78, 186, 111;
          45, 149, 191;
          149, 91, 165]/255;

set(groot, 'DefaultAxesColor', [25, 25, 25]/255);
set(groot, 'DefaultFigureColor', [25, 25, 25]/255);
set(groot, 'DefaultFigureInvertHardcopy', 'off');

set(groot, 'DefaultAxesColorOrder', colors);
set(groot, 'DefaultLineLineWidth', 3);

set(groot, 'DefaultTextInterpreter', 'LaTeX');
set(groot, 'DefaultAxesTickLabelInterpreter', 'LaTeX');
set(groot, 'DefaultAxesFontName', 'LaTeX');
set(groot, 'DefaultLegendInterpreter', 'LaTeX');


% Figure 1
figure(1);

x = linspace(0, 20, 1000);

hold on;
for i = 0:4
    plot(x, besselj(i, x));
end
title('Plot of $J_{\nu}(x)$ for $\nu = 0\dots 4$');

% Figure 2
figure(2);

x = linspace(-1, 1, 1000);

hold on;
for i=1:5
    plot(x, legendreP(i,x));
end
title('Plot of $P_i(x)$ for $i=1 \dots 5$');
{% endhighlight %}

## Resetting the Factory Default Properties

If you decide you want to reset the properties back to the Matlab factory defaults, you may notice that the standard plot clear commands (`clf('reset')`, and `cla reset`) do not clear the properties. Running `set(groot, 'Default', struct())` will clear all custom default settings you have applied and the factory settings will be used.

## My Default Settings
Here are the default settings I typically use. A sample plot with only these settings applied can be seen in <a href="#Figure4" >Figure 4</a>. You can add these to a <a href="https://www.mathworks.com/help/matlab/ref/startup.html" target="_blank">startup file</a> so they are automatically applied every time you open Matlab.

{% highlight matlab %}
set(groot, 'DefaultLineLineWidth', 3, ...
           'DefaultTextInterpreter', 'LaTeX', ...
           'DefaultAxesTickLabelInterpreter', 'LaTeX', ...
           'DefaultAxesFontName', 'LaTeX', ...
           'DefaultLegendInterpreter', 'LaTeX', ...
           'DefaultAxesLineWidth', 1.5, ...
           'DefaultAxesFontSize', 16, ...
           'DefaultAxesBox', 'on', ...
           'DefaultAxesColor', [1, 1, 1], ...
           'DefaultFigureColor', [1, 1, 1], ...
           'DefaultFigureInvertHardcopy', 'off', ...
           'DefaultFigurePaperUnits', 'inches', ...
           'DefaultFigureUnits', 'inches', ...
           'DefaultFigurePaperPosition', [0, 0, 5, 3.09], ...
           'DefaultFigurePaperSize', [5, 3.09], ...
           'DefaultFigurePosition', [0.1, 0.1, 4.9, 2.99]);
{% endhighlight %}

<figure class="align-center" style="width: 75%;">
    <a name="Figure3"></a>
    <img src="{{ site.asset_url }}/Default_Plot_Settings_in_Matlab/Figure4.png"    
         srcset="{{ site.asset_url }}/Default_Plot_Settings_in_Matlab/Figure4.png 1x, {{ site.asset_url }}/Default_Plot_Settings_in_Matlab/Figure4%402x.png 2x" 
       alt="Figure 4" 
       width="708" 
       height="216">
  <figcaption><b>Figure 4:</b> Sample plots with my default plot appearance settings applied.
  </figcaption>
</figure>

## Final Thoughts
It takes a little bit of work to setup your own default plot settings in Matlab. Hopefully in the future Matlab will implement themes similar to how some of the popular plotting libraries work (ggplot, Plotly, Seaborn, etc.). I hope this article was helpful!

