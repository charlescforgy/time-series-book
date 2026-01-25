# 3.4 Autocorrelation

## Autocorrelation Definition

As discussed in [Chapter 2](../chapter_2_background_math/04_variance_covariance.md#drawbacks-to-covariance), covariance, and by extension autocovariance, has some undesirable characteristics. For this reason, in time series analysis we often prefer to work with the *autocorrelation*, which is a unitless quantity bounded by $[-1,1]$.

:::{warning} Differing Definitions
As with autocovariance in the [second section of this chapter](02_autocovariance.md#autocovariance-definition), the definition of autocorrelation we use is the one from statistics. This definition differs slightly from the one used in disciplines such as electrical engineering.
:::

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

::::{tip} Problem
Find $\rho(h)$ for $h=0,1,2,\ldots$ for the moving average $v_t$ from [Chapter 1](../chapter_1_introduction/04_basic_models.md#simple-three-value-moving-average).

:::{dropdown} Click to reveal solution
**Solution:** In the [previous section](03_stationarity.md#autocovariance-of-a-stationary-process) we demonstrated that $v_t$ is both weakly and strictly stationary. Consequently, $\rho(h)=\frac{\gamma(h)}{\gamma(0)}$. Drawing on the autocovariance from the [second section](02_autocovariance.md#mean-and-autocovariance-of-moving-average), we have:

$$
\begin{equation}
	    \rho(h)=\begin{cases}
		\frac{\frac{3}{9}\sigma_w^2}{\frac{3}{9}\sigma_w^2}=1, & h=0\\[7pt]
		\frac{\frac{2}{9}\sigma_w^2}{\frac{3}{9}\sigma_w^2}=\frac{2}{3}, & h=1\\[7pt]
		\frac{\frac{1}{9}\sigma_w^2}{\frac{3}{9}\sigma_w^2}=\frac{1}{3}, & h=2\\[7pt]
		0, & \text{otherwise.}\\
	\end{cases}
\end{equation}
$$ (moving-average-acorf)

Eq. {eq}`moving-average-acorf` demonstrates an additional advantage of the autocorrelation in that it does not require us to know anything about the variance $\sigma_w^2$ beyond the requirement that it be finite.
:::
::::

## Autocorrelation is Positive Semidefinite

The autocorrelation matrix $\mathbf{P}$ is related to the autocovariance matrix $\boldsymbol{\Gamma}$ by

$$
\mathbf{P} = \frac{1}{\gamma(0)}\boldsymbol{\Gamma}.
$$

$\gamma(0)$ is the variance, and hence cannot be negative, so $\mathbf{v}^T\mathbf{P}\mathbf{v}$ will only differ from $\mathbf{v}^T\boldsymbol{\Gamma}\mathbf{v}$ by a positive constant term given by $\frac{1}{\gamma(0)}$. As a result, since $\boldsymbol{\Gamma}$ [is positive semidefinite](03_stationarity.md#autocovariance-of-a-stationary-process-is-positive-semidefinite), $\mathbf{P}$ must also be as well.

## Autocorrelation Plots

It is often instructive to plot the autocorrelation of a time series. `statsmodels` has a nice built-in function to create plots:
:::{code-cell} ipython3
from statsmodels.graphics.tsaplots import plot_acf
plot_acf(my_time_series, lags=20)
:::
where the parameter `lags` controls the maximum value of $h$. `lags=20` is generally a good starting choice for most scenarios, though for longer patterns such as hourly data with a 24-hour period you may want to set lags to something like 72 or 96.

`plot_acf` has other arguments that we will discuss [once we've covered more theory](06_estimation.md#sample-autocovariance). For now, let's take a look at a couple of different autocorrelation plots to get a feeling for how they behave.

:::{figure} images/acf_ma.png
---
width: 95%
name: ma2-fig
---
Sample autocorrelation plot from `statsmodels` with sudden cutoff point. Note that lag 0 is always equal to 1.
:::

{ref}`ma2-fig` shows two significant lags (plus the value of 1 at $h=0$), and falls to statistical insignificance thereafter as denoted by the blue shading. In Chapter 5 we will see that this is a hallmark characteristic of a *moving average* process.

:::{figure} images/acf_ar.png
---
width: 95%
name: ar1-fig
---
Sample autocorrelation plot from `statsmodels` with exponential decay.
:::

{ref}`ar1-fig` demonstrates an exponentially decaying autocorrelation that remains statistically significant even at $h=20$. In Chapter 4 we will see that this behavior corresponds to an *autoregressive* process.

::::{tip} Problem
One of the best ways to learn time series analysis is to experiment with time series. For this problem, download the non-seasonally adjusted US employment rate from the St. Louis Federal Reserve bank and examine the raw data as follows:

:::{code-cell} ipython3
import pandas as pd
import matplotlib.pyplot as plt
from statsmodels.graphics.tsaplots import plot_acf

df = pd.read_csv("https://fred.stlouisfed.org/graph/fredgraph.csv?bgcolor=%23ebf3fb&chart_type=line&drp=0&fo=open%20sans&graph_bgcolor=%23ffffff&height=450&mode=fred&recession_bars=on&txtcolor=%23444444&ts=12&tts=12&width=1140&nt=0&thu=0&trc=0&show_legend=yes&show_axis_titles=yes&show_tooltip=yes&id=U6RATENSA&scale=left&cosd=1994-01-01&coed=2025-11-01&line_color=%230073e6&link_values=false&line_style=solid&mark_type=none&mw=3&lw=3&ost=-99999&oet=99999&mma=0&fml=a&fq=Monthly&fam=avg&fgst=lin&fgsnd=2020-02-01&line_index=1&transformation=lin&vintage_date=2026-01-07&revision_date=2026-01-07&nd=1994-01-01",
                 index_col=0,
                 parse_dates=True,
                 )
# statsmodels is very particular regarding missing values, we will fill in missing values with the previous value.
df.ffill(inplace=True)
# examine a quick plot
plt.plot(df)
:::

What factors do you observe in the plot? Can you pick out signs of the pandemic?

Next, lets plot the autocorrelation function:

:::{code-cell} ipython3
# Assigning the plot to "fig" prevents Jupyter from printing the plot twice.
fig = plot_acf(df, lags=48) # four years of autocorrelation
fig.show()
:::

What do you observe with regard to the autocorrelation? Does it resemble either {ref}`ma2-fig` or {ref}`ar1-fig`?
::::
