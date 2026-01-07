# Autocorrelation

## Autocorrelation Definition

As discussed in [Chapter 2](../chapter_2_background_math/04_variance_covariance.md#drawbacks-to-covariance), covariance, and by extension autocovariance, has some undesirable characteristics. For this reason, in time series analysis we often prefer to work with the *autocorrelation*, which is a unitless quantity bounded by $[-1,1]$.

```{warning}
As with autocovariance in the [second section of this chapter](02_autocovariance.md#autocovariance), the definition of autocorrelation we use is the one from statistics. This definition differs slightly from the one used in disciplines such as electrical engineering.
```

For any finite variance process, the autocorrelation is simply the autocovariance normalized by the the variances.

$$
\begin{equation}
\rho(s,t) = \frac{\gamma(s,t)}{\sqrt{\gamma(s,s)\gamma(t,t)}}.
\end{equation}
$$

When considering stationary time series, we can employ the simplifications

$$
\gamma(s,t)=\gamma(|s-t|) =\gamma(h),
$$

and

$$
\gamma(s,s)=\gamma(t,t)=\gamma(0).
$$

As such, a stationary time series has autocorrelation given by

$$
\begin{equation}
\rho(h) = \frac{\gamma(h)}{\gamma(0)}.
\end{equation}
$$

Note that $\rho(h)=\rho(-h)$ and $\rho(0)=1$.

## Autocorrelation Plots

It is often instructive to plot the autocorrelation of a time series. `statsmodels` has a nice built-in function to create plots:

```{code-cell} ipython3
from statsmodels.graphics.tsaplots import plot_acf
plot_acf(my_time_series, lags=20)
```
where the parameter `lags` controls the maximum value of $h$. `lags=20` is generally a good choice for most scenarios, though for longer patterns such as hourly data with a 24 hour period you may want to set lags to something like 72 or 96.

`plot_acf` has other arguments that we will discuss once we've covered more theory. For now, let's take a look at a couple of different autocorrelation plots to get a feeling for how they behave.

```{figure} images/ma2_acf.png
---
width: 95%
name: ma2-fig
---
Sample autocorrelation plot from `statsmodels` with sudden cutoff point. Note that lag 0 is always equal to 1.
```

{ref}`ma2-fig` shows two significant lags (plus the value of 1 at $h=0$), and falls to statistical insignificance thereafter as denoted by the blue shading. In Chapter 4 we will see that this is a hallmark characteristic of a *moving average* process.

```{figure} images/ar1_acf.png
---
width: 95%
name: ar1-fig
---
Sample autocorrelation plot from `statsmodels` with exponential decay.
```

{ref}`ar1-fig` demonstrates an exponentially decaying autocorrelation that only drops to statistical insignificance around $h=40$. In Chapter 4 we will see that this behavior corresponds to an *autoregressive* process.