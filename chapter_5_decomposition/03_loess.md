# 5.3 Local Regression

## Kernel Smoothing

We've encountered data smoothing in the form of a moving average. {ref}`white-noise-fig-2` and {ref}`smoothed-noise-fig-2` present a synthetic white noise process and the same process after smoothing with a moving average of length $10$.

```{figure} images/white_noise.png
---
width: 95%
name: white-noise-fig-2
---
Simulated white noise process.
```

```{figure} images/smoothed_white_noise.png
---
width: 95%
name: smoothed-noise-fig-2
---
Smoothed version of white noise after applying the filter $[\frac{1}{10}, \frac{1}{10},...,\frac{1}{10}]$.
```

We can think of the smoothing process depicted in {ref}`smoothed-noise-fig-2` as consisting of sliding a *kernel* (also known as a *filter*) across the data. In this case, the kernel is defined as

$$
\begin{equation}
\Bigg[\frac{1}{10}, \frac{1}{10},\frac{1}{10}, \frac{1}{10},\frac{1}{10},\frac{1}{10}, \frac{1}{10},\frac{1}{10}, \frac{1}{10},\frac{1}{10}\Bigg]
\end{equation}
$$ (boxcar-filter)

which we can envision as being slid across the data. At each point, we take the inner (or dot) product of the data with the kernel. For the kernel used in Eq. {eq}`boxcar-filter` this gives the unweighted average of $10$ points. In later chapters we will learn that this can be thought of as a *convolution* in the frequency domain.

The box (or boxcar) kernel used in {ref}`smoothed-noise-fig-2` is only one of many possible choices. We will learn in future chapters that a box filter has substantial shortcomings in the frequency domain that are mitigated (though not eliminated) by use of tapered kernels, such as kernels based on Gaussians, the cosine function, or the tricube function introduced in the next section.

### Tricube Function

For this chapter, the most important kernel method is generated from the *tricube function* $W$:

$$
\begin{equation}
	W(x_0, x_i) \stackrel{\triangle}= \begin{cases}
	(1-|u|^3)^3 & |u| \leq 1 \\
	0  &  |u| > 1  \\
	\end{cases}
\end{equation}
$$ (tricube-def)

where $u$ is the distance between the the focal point $x_0$ and the point $x_i$ calculated as $u=\frac{x_0-x_i}{\max_j{(|x_0-x_j|)}}$[^1]. The maximum $j$ is determined by an adjustable parameter giving the length of the kernel.

[^1]: The quantity $\max_j{(|x_0-x_j|)}$ is often referred to as the *bandwidth*. We will reserve the term bandwidth for use in the context of the frequency domain.

:::{important} Normalization of Kernel
Note that in Eq. {eq}`tricube-def` we have not normalized the overall window to a value of $1$ as we have done with moving average kernels. The reason for this distinction will become clear once we discuss the tricube function's use in the context of linear or polynomial regression below.
:::

For example, for $\max_j{(|x_0-x_j|)}=5$, the tricube function $W$ generates a sharply peaked kernel with $2j-1=9$ non-zero values out of $2j+1=11$ total values

$$
\begin{equation}
\big[0, 0.116, 0.482, 0.820, 0.976, 1, 0.976, 0.820, 0.482, 0.116,0 \big]
\end{equation}
$$ (tricube-filter)

with all points outside the window weighted as $0$ (note that for the first and last elements $u=\frac{\pm5}{5}$, resulting in zero for $W$). In actual implementation we would simply use a kernel of length $2j-1$ ($9$, in our example) instead of using a kernel of length $2j+1$ ($11$, in our example) with zeros in the first and last positions. We explicitly included the zeros in Eq. {eq}`tricube-filter` to emphasize that the kernel falls to zero at $|u|=1$.
	 	
In order to avoid shortening the time series (as occurs with moving averages), any point less than $\max_j{(|x_0-x_j|)}$ from either end has an asymmetric selection of points in order to maintain the same kernel size at the expense of a larger value for $\max_j{(|x_0-x_j|)}$. For example, continuing with $\max_j{(|x_0-x_j|)}=5$ from before, the asymmetric window used for the first point in the series will now have $\max_j{(|x_0-x_j|)}=9$ with values given as:

$$
\begin{equation}
\big[1, 0.996, 0.967, 0.893, 0.759, 0.569, 0.348, 0.148, 0.0264, 0  \big]
\end{equation}
$$ (tricube-filter-begin)

## LOWESS Algorithm

Local regression is perhaps best thought of as a hybrid between kernel smoothing and linear regression (or more broadly polynomial regression). The most common family of methods is LOWESS (locally weighted scatterplot smoothing), a type of LOESS (locally estimated scatterplot smoothing)[^2]. The LOWESS algorithm is generally credited as having been formally introduced in [](https://doi.org/10.1080/01621459.1979.10481038) and [](https://doi.org/10.2307/2683591) (though much of the concept had been proposed independently by [](https://doi.org/10.1021/ac60214a047))

[^2]: Some sources such as [NIST](https://www.itl.nist.gov/div898/handbook/pmd/section1/pmd144.htm) treat the terms LOESS and LOWESS as being interchangeable. Cleveland himself in [](https://doi.org/10.1080/01621459.1988.10478639) appears to have used the term LOESS as a multivariate generalization of the previous univariate LOWESS.

### LOWESS Demo

Before diving into the underlying algorithm, let's get a feeling for how LOWESS behaves. The following interactive figure plots the function

$$
y = \sin{(x)} + \frac{1}{2}\sin{(3x)} + w
$$

for some noise $w$ in gray dots. For reference, the original (presumably unknown) clean signal without noise that we are attempting to reconstruct is denoted by a gray dashed line. The toggle button `LOWESS CURVE` turns on or off the LOWESS curve fitted through the points. The button `LOCAL FITS` toggles on and off a selection of the individual local regression lines (more on those soon). Finally, the slider `SPAN (f)` tells the algorithm what fraction of the overall data to consider for each local fit. Note that at $1.00$, the line is almost a flat linear regression line with large local regression lines. As you shrink the fraction down, the LOWESS fit becomes more jagged with smaller local regression lines (denoting that the local line is calculated with a smaller portion of the data).

<iframe src="/time-series-book/lowess_demo.html" width="100%" height="520" frameborder="0" scrolling="no"></iframe>

If the above fails to render correctly in your browser you can also open the demo as a new browser window using the `Open Demo in a New Tab ↗` button at the top of the frame. Note that you may need to enable popups for this to work.

:::{tip} Problem
What fraction in `SPAN (f)` does the best job reconstructing the original signal?
:::



