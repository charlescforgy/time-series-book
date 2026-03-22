# 5.3 Local Regression

## Kernel Smoothing

## LOWESS Algorithm

Local regression is perhaps best thought of as a hybrid between kernel smoothing and linear regression (or more broadly polynomial regression). The most common family of methods is LOWESS (locally weighted scatterplot smoothing), a type of LOESS (locally estimated scatterplot smoothing)[^1]. The LOWESS algorithm is generally credited as having been formally introduced in [](https://doi.org/10.1080/01621459.1979.10481038) and [](https://doi.org/10.2307/2683591) (though much of the concept had been proposed seperately by [](https://doi.org/10.1021/ac60214a047))

[^1]: Some sources such as [NIST](https://www.itl.nist.gov/div898/handbook/pmd/section1/pmd144.htm) treat the terms LOESS and LOWESS as being interchangeable. Cleveland himself in [](https://doi.org/10.1080/01621459.1988.10478639) appears to have used the term LOESS as a multivariate generalization of the previous univariate LOWESS.

### LOWESS Demo

Before diving into the underlying algorithm, let's get an feeling for how LOWESS behaves. The following interactive figure plots the function

$$
y = \sin{(x)} + \frac{1}{2}\sin{(3x)} + w
$$

for some noise $w$ in gray dots. For reference, the original (presumably unknown) clean signal without noise that we are attempting to reconstruct is denoted by a gray dashed line. The toggle button `LOWESS CURVE` turns on or off the LOWESS curve fitted through the points. The button `LOCAL FITS` toggles on and off a selection of the individual local regression lines (more on those soon). Finally, the slider `SPAN (f)` tells the algorithm what fraction of the overall data to consider for each local fit. Note that at $1.00$, the line is almost a flat linear regression line with large local regression lines. As you shrink the fraction down, the LOWESS fit becomes more jagged with smaller local regression lines (denoting that the local line is calculated with a smaller portion of the data).

<iframe src="/time-series-book/lowess_demo.html" width="100%" height="1250" frameborder="0"></iframe>


:::{tip} Problem
What fraction in `SPAN (f)` does the best job reconstructing the original signal?
:::