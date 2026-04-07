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

### Weighting Points Near the Edges

In order to avoid shortening the time series (as occurs with moving averages), any point less than $\max_j{(|x_0-x_j|)}$ from either end has an asymmetric selection of points in order to maintain the same kernel size at the expense of a larger value for $\max_j{(|x_0-x_j|)}$. For example, continuing with $\max_j{(|x_0-x_j|)}=5$ from before, the asymmetric window used for the first point in the series will now have $\max_j{(|x_0-x_j|)}=9$ with values given as:

$$
\begin{equation}
\big[1, 0.996, 0.967, 0.893, 0.759, 0.569, 0.348, 0.148, 0.0264, 0  \big]
\end{equation}
$$ (tricube-filter-begin)

## LOWESS Algorithm

Local regression is perhaps best thought of as a hybrid between kernel smoothing and linear regression (or more broadly polynomial regression). The most common family of methods is LOWESS (locally weighted scatterplot smoothing), a type of LOESS (locally estimated scatterplot smoothing)[^2]. The LOWESS algorithm is generally credited as having been formally introduced in [Cleveland (1979)](https://doi.org/10.1080/01621459.1979.10481038) and [Cleveland (1981)](https://doi.org/10.2307/2683591) (though much of the concept had been proposed independently by [Savitzky & Golay (1964)](https://doi.org/10.1021/ac60214a047) about fifteen years beforehand).

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


### Local Regressions

The LOWESS algorithm begins by dividing the data into overlapping windows. The size of each window is governed by the parameter $f$, the fraction of the total observations $n$. In `statsmodels.nonparametric.smoothers_lowess.lowess`, the fraction is set with the argument `frac` (default `frac=2.0/3.0`). In the demo above, $f$ can be smoothly varied from $0.05$ to $1$. Once $f$ is chosen, we create $n$ windows of length $\lceil f\times n \rceil$ centered at each data point where $\lceil f\times n \rceil$ indicates the smallest integer greater than $f\times n$. For each window about a given central point (denoted as $x_{center}$), we calculate a weighted linear regression defined as

$$
\begin{equation}
	\min_{\beta_0, \beta_1} \sum_{i=1}^{\lceil f\times n \rceil}W(x_{center}, x_i) (y_i-\beta_0(x_{center})-\beta_1(x_{center})x_i)^2
\end{equation}
$$ (local-linear-regression)

where $W(x_{center}, x_i)$ is a weight function, most commonly the [tricube function](#tricube-function), that weights the contribution of each $x_i$ based on its distance from $x_{center}$ and where we have expressed the coefficients as $\beta_0(x_{center})$ and $\beta_1(x_{center})$ to emphasize that distinct $\beta$'s are calculated for each data point. For points closer than $\frac{f\times n}{2}$ to either end, the window no longer places the point in the center of the window, and uses the modification to $W$ [discussed above](#weighting-points-near-the-edges).

LOWESS can be extended from linear regression to polynomial regression using expressions such as

$$
\begin{equation}
	\min_{\beta_0, \beta_1,\beta_2} \sum_{i=1}^{\lceil f\times n \rceil}W(x_{center}, x_i) (y_i-\beta_0(x_{center})-\beta_1(x_{center})x_i-\beta_2(x_{center})(x_i)^2)^2,
\end{equation}
$$

though we will not need to go beyond Eq. {eq}`local-linear-regression` for our purposes.

Having calculated $n$ distinct linear regressions, we use each linear regression about a given $x_{center}$ to calculate the $\hat{y}_{center}$ at that point, leaving us with $n$ $\hat{y}$ values (one per data point). The $\hat{y}$ points are then connected into a single curve—though note in the demo above that the fitted curve becomes notably more jagged as $f$ is decreased.

### Robust Smoothing

In the [demo above](#lowess-demo), we calculated the LOWESS curve solely by implementing Eq. {eq}`local-linear-regression`. This method works well for data that has relatively few outliers. [Cleveland (1979)](https://doi.org/10.1080/01621459.1979.10481038) describes an optional second stage of smoothing using the *bisquare function* $B(x)$ defined as

$$
\begin{equation}
	B(x) \stackrel{\triangle}= \begin{cases}
	(1-x^2)^2 & |x| \leq 1 \\
	0  &  |x| > 1  \\
	\end{cases}
\end{equation}
$$ (bisquare-def)

where as in Eq. {eq}`tricube-def` $B(x)$ falls to zero at $x=\pm1$. After the initial LOWESS curve calculation using Eq. {eq}`local-linear-regression`, the algorithm then goes through a predetermined number of iterations of the following procedure:

1. Calculate the residual between the predicted and observed points
$$
e_i = y_i -\hat{y}_i.
$$
2. Determine new weightings $\delta$ for each point by first calculating the median $s$ of the $|e_i|$'s and then applying the formula
$$
\delta_k \stackrel{\triangle}{=} B\Big(\frac{e_k}{6s}\Big).
$$
3. Recalculate the local regressions using Eq. {eq}`local-linear-regression` using $B\times W$ instead of $W$.

Cleveland refers to this algorithm as "robust locally weighted regression." In `statsmodels`, the number of iterations of the above procedure is controlled by  the argument `it` (default `it=3`). Setting `it=0` will result in the algorithm
only using Eq. {eq}`local-linear-regression` in its original form.

In general, robust smoothing is primarily required for datasets with large outliers, such as those produced by Cauchy distributed noise. {ref}`robust-lowess-cauchy` demonstrates LOWESS smoothing with different numbers of iterations on a synthetic dataset with Cauchy distributed noise. Note in particular that for $0$ iterations (non-robust LOWESS) the fitted LOWESS line "chases" the outliers out of the plot. Once we reach $3$ iterations, the LOWESS line almost perfectly captures the original signal.

:::{figure} images/robust_lowess_cauchy.png
---
width: 95%
name: robust-lowess-cauchy
---
LOWESS regression using $0, 1, 3,$ and $6$ iterations of robust smoothing on synthetic dataset defined as $y=\sin{(x)}+w$ with $w$ Cauchy distributed.
:::

::::{tip} Problem
Generate your own version of {ref}`robust-lowess-cauchy` using the following code:

:::{code-cell} ipython3
import numpy as np
from statsmodels.nonparametric.smoothers_lowess import lowess
import matplotlib.pyplot as plt

rng_cauchy = np.random.default_rng(17)
x_cauchy = np.linspace(0, 10, 200)
y_cauchy = np.sin(x_cauchy) + np.random.standard_cauchy(size=len(x_cauchy)) * 0.3

# It is advisable to run the following in a new cell to avoid generating new datasets for each new plot.
smoothed_data = lowess(exog=x_cauchy, endog=y_cauchy, frac=0.2, it=3, return_sorted=True)
x_lowess, y_lowess = smoothed_data[:, 0], smoothed_data[:, 1]
lowess_fig, lowess_ax = plt.subplots()
lowess_ax.scatter(x_cauchy, y_cauchy, s=8, alpha=0.3, color="#888780", label="Observations")
lowess_ax.plot(x_lowess, y_lowess, label="LOWESS Curve")
lowess_ax.plot(x_cauchy, np.sin(x_cauchy), "k--", lw=1.5, alpha=0.6, label="True signal")
lowess_ax.set_ylim(-3,3)
lowess_ax.set_xlim(0,10)
plt.legend()
plt.show()
:::
What happens to the fitted curve as you adjust `it`? What if you adjust `frac`?
::::