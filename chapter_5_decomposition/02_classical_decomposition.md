# 5.2 Classical Decomposition

## Components of Time Series

Many time series contain [trends](../chapter_4_enforcing_stationarity/02_trend.md#definition-of-trend), [seasonal effects](../chapter_4_enforcing_stationarity/04_seasonality.md#seasonality-definition), and/or [cycles](../chapter_4_enforcing_stationarity/04_seasonality.md#seasonal-vs-cyclic-changes). *Time series decomposition*, or simply *decomposition*, is defined as methods to isolate each component's contribution to an overall time series. Since cycles lack a specific length it is difficult to draw a clear distinction between an upward trend followed by a downward trend and a cycle. Consequently, we will generally fold cycles into the trend for the purposes of decomposition.

:::{note} Trends and Cycles in Decomposition
Most time series decomposition methods combine trends and cycles into a single component known as the "trend-cycle," or often merely as the "trend."
:::

Before diving into the details, let's get a feeling for what a time series decomposition might look like.

:::{figure} images/stl_decomp_unemployment.png
---
width: 95%
name: stl-decomp-unemployment
---
US employment rate from 1948 through 2025 from the [Federal Reserve Bank of St. Louis](https://fred.stlouisfed.org/series/UNRATENSA) with STL decomposition consisting of original series (top), trends and cycles (second from top), seasonal contribution (third from top), and residual variation not explained (bottom).
:::

We'll delve into the algorithms used to create figures like {ref}`stl-decomp-unemployment` below and in subsequent chapters. For now, let's take a moment to understand the components depicted in {ref}`stl-decomp-unemployment`.

1. $T_t$: The *trend component*, consisting of both trends and cycles in the data.
2. $S_t$: The *seasonal component*, consisting of regular periodicity at a specified seasonal length. Note that most decomposition methods only allow a single seasonality.
3. $R_t$: The *residual* (sometimes also referred to as the *remainder*), consisting of any variation not accounted for by $T_t$ or $S_t$. The residual can be thought of as roughly equivalent to noise $w_t$, though I'd caution against taking this analogy too far. $R_t$ is only strictly equivalent to noise if $T_t$ and $S_t$ are a perfect model for the underlying process.

### Additive and Multiplicative Models

Most commonly, we assume an additive decomposition in the form of

$$
\begin{equation}
 	x_t = S_t + T_t + R_t.
\end{equation}
$$ (additive-decomp)

In some cases, we may also assume a multiplicative decomposition

$$
\begin{equation}
 	x_t = S_t \times T_t \times R_t,
\end{equation}
$$ (mult-decomp)
which is more applicable, for example, in forecasting quarterly sales of a product experiencing compound growth in demand such as [Mac sales in the previous chapter](../chapter_4_enforcing_stationarity/05_log_transform.md#apple-mac-sales). Note that we can always recast Eq. {eq}`mult-decomp` into the form of Eq. {eq}`additive-decomp` by using a log transform

$$
\begin{equation}
 	\log{(x_t)} = \log{(S_t)} + \log{(T_t)} + \log{(R_t)}.
\end{equation}
$$ (log-decomp)


While other models are possible, for example

$$
x_t = S_t + T_t \times R_t,
$$

such models are almost never used in practice if for no other reason than that the difficulty in interpreting the output  can easily defeat the original purpose of time series decomposition.

## Classical Decomposition Algorithm

:::{important} Obsolete Methodology
The classical time series decomposition models are essentially obsolete and have been supplanted by new methods such as STL decomposition. We study the classical method here primarily as a basis for understanding STL decomposition.
:::

### Defining Seasonal Length

Before using the algorithm we must first define our seasonal period $m$. Common values of $m$ include $m=7$ for weekly data, $m=12$ for monthly data, and $m=24$ for hourly data. Note that we cannot define multiple seasonal effects in a given decomposition.

Recall that a moving average with [the length of a season (or any multiple thereof)](../chapter_4_enforcing_stationarity/04_seasonality.md#seasonal-moving-average) will remove seasonal effects, allowing us to focus on longer term cycles and trends. For seasonal effects with odd numbers of time steps per season such as a $m=7$ for weekly seasonality, we employ a simple centered moving average of length $m$. Thus, for data starting on a Sunday, the first Wednesday will be replaced with the average of the first Sunday-Saturday, the first Thursday will be replaced by the average of the the first Monday-Sunday, and so on. Note the we do lose the first and last $\frac{m-1}{2}$ observations for odd $m$ or—as we will see shortly—the first and last $\frac{m}{2}$ for even $m$. This is not an issue for long time series in which the number of observations $n>>m$, but does pose a challenge for shorter time series.

:::{table} $7-$Day Moving Average


| Original Day of Week | Value in Smoothed Series |
| --- | --- |
|Sunday | —|
|Monday | —|
|Tuesday |— |
|Wednesday | $\frac{1}{7}(\text{Sunday}+\text{Monday}+\text{Tuesday}+\text{Wednesday}+\text{Thursday}+\text{Friday}+\text{Saturday})$ |
|Thursday | $\frac{1}{7}(\text{Monday}+\text{Tuesday}+\text{Wednesday}+\text{Thursday}+\text{Friday}+\text{Saturday}+\text{Sunday})$ |
|Friday | $\frac{1}{7}(\text{Tuesday}+\text{Wednesday}+\text{Thursday}+\text{Friday}+\text{Saturday}+\text{Sunday}+\text{Monday})$ |
|$\vdots$ |$\vdots$|
|Monday | $\frac{1}{7}(\text{Friday}+\text{Saturday}+\text{Sunday}+\text{Monday}+\text{Tuesday}+\text{Wednesday}+\text{Thursday})$ |
|Tuesday | $\frac{1}{7}(\text{Saturday}+\text{Sunday}+\text{Monday}+\text{Tuesday}+\text{Wednesday}+\text{Thursday}+\text{Friday})$ |
|Wednesday |$\frac{1}{7}(\text{Sunday}+\text{Monday}+\text{Tuesday}+\text{Wednesday}+\text{Thursday}+\text{Friday}+\text{Saturday})$  |
|Thursday | —|
|Friday |— |
|Saturday |— |
:::

For even values of $m$ such as quarterly data, it is impossible to center of moving average of length $m$. As a compromise, we use a $2\times m$ moving average. A $2\times m$ moving average is a moving average of moving averages, for example, a $2\times 4$ moving average is defined as:

$$
\begin{equation}
 	\begin{split}
 	\hat{T}_t &= \frac{1}{2} \Big(\frac{1}{4} [x_{t-2}+x_{t-1}+x_t+x_{t+1}] + \frac{1}{4} [x_{t-1}+x_{t}+x_{t+1}+x_{t+2}]\Big)\\
 	&=\frac{1}{8}x_{t-2} + \frac{1}{4}x_{t-1} + \frac{1}{4}x_{t} + \frac{1}{4}x_{t+1} + \frac{1}{8}x_{t+2} 
 	\end{split}
\end{equation}
$$ (2-by-m-window)

A $2\times m$ moving average allows us to center each observation by using an odd $m+1$ length window, weighting the first and last observation by $\frac{1}{2m}$ and the others by $\frac{1}{m}$.

:::{table} Quarterly Moving Average

| Original Quarter | Value in Smoothed Series |
| --- | --- |
|First | —|
|Second | —|
|Third | $\frac{1}{8}\text{First} + \frac{1}{4}\text{Second} + \frac{1}{4}\text{Third} + \frac{1}{4}\text{Fourth} + \frac{1}{8}\text{First}$ |
|Fourth | $\frac{1}{8}\text{Second} + \frac{1}{4}\text{Third} + \frac{1}{4}\text{Fourth} + \frac{1}{4}\text{First} + \frac{1}{8}\text{Second}$  |
|$\vdots$|$\vdots$|
|First |  $\frac{1}{8}\text{Third} + \frac{1}{4}\text{Fourth} + \frac{1}{4}\text{First} + \frac{1}{4}\text{Second} + \frac{1}{8}\text{Third}$|
|Second | $\frac{1}{8}\text{Fourth} + \frac{1}{4}\text{First} + \frac{1}{4}\text{Second} + \frac{1}{4}\text{Third} + \frac{1}{8}\text{Fourth}$ |
|Third | —|
|Fourth |— |
:::

::::{tip} Problem
Why do we use a $2\times m$ window for a series with an even $m$ instead of an $m+1$ window?

:::{dropdown} Click to reveal solution
**Solution:** As discussed, our objective in using [a moving average with a window the same length as the seasonality is to remove seasonal effects](../chapter_4_enforcing_stationarity/04_seasonality.md#seasonal-moving-average). Even though a $2\times m$ window has length $m+1$, it's actually composed of two moving averages of length $m$ as shown in Eq. {eq}`2-by-m-window`, each of which alone has already smoothed out the seasonality (the second layer of averaging is only necessary to center our observation). In contrast, an $m+1$ window is not a multiple of $m$, and hence will not remove seasonal effects.
:::
::::

The smoothed series is used as our estimate of the series trend $T_t$, denoted as $\hat{T}_t$ to emphasize that it is an estimate to the true $T_t$. From here on, classical decomposition subdivides into additive and multiplicative methods.

::::{tip} Problem
While `pandas` can easily produce a centered moving average for data will odd seasonality, using a $2\times m$ window requires a second step. In this exercise we will use the `seasonal_decompose` module from `statsmodels` to automatically apply the correct moving average window based on $m$.

:::{code-cell} ipython3
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
from statsmodels.tsa.seasonal import seasonal_decompose

employment_df = pd.read_csv("https://fred.stlouisfed.org/graph/fredgraph.csv?bgcolor=%23ebf3fb&chart_type=line&drp=0&fo=open%20sans&graph_bgcolor=%23ffffff&height=450&mode=fred&recession_bars=on&txtcolor=%23444444&ts=12&tts=12&width=1320&nt=0&thu=0&trc=0&show_legend=yes&show_axis_titles=yes&show_tooltip=yes&id=UNRATENSA&scale=left&cosd=1948-01-01&coed=2026-01-01&line_color=%230073e6&link_values=false&line_style=solid&mark_type=none&mw=3&lw=3&ost=-99999&oet=99999&mma=0&fml=a&fq=Monthly&fam=avg&fgst=lin&fgsnd=2020-02-01&line_index=1&transformation=lin&vintage_date=2026-02-24&revision_date=2026-02-24&nd=1948-01-01", index_col=0)
employment_df.index = pd.date_range(start=employment_df.index[0], end=employment_df.index[-1], freq="MS")
employment_df.columns = ["Unemployment"]
employment_df.ffill(inplace=True)

# Decompose the time series to access the trend component. Be sure to specify the correct period (12, in this case).
classic_decomp = seasonal_decompose(employment_df["Unemployment"], model="additive", period=12)

# Get the trend component and plot it.
employment_df["Moving Average"] = classic_decomp.trend
plt.plot(employment_df)
:::
Does your plot look like its removed seasonality? What do you observe regarding trends and cycles?
::::

### Additive Method

Having obtained our estimate of the trend $\hat{T}_t$, we are now ready to estimate the seasonality. We first detrend the series by subtracting $\hat{T}_t$

$$
\begin{equation}
	x_{t, detrend} = x_t - \hat{T}_t
\end{equation}
$$ (detrend-eq)

We then obtain our estimate of the seasonal component $\hat{S}_t$ by taking the average of each detrended value of that season (e.g. the average detrended Thursday or average detrended May). The individual seasonal components are adjusted for an overall baseline of zero, i.e.

$$
\sum_{t}\hat{S}_t \approx 0.
$$

For example, our baseline temperature might be $60°\, F$, with the summer being $30°$ higher and the winter $30°$ lower.

::::{tip} Problem
This exercise continues with the results of classical decomposition from the previous problem.

:::{code-cell} ipython3

# Get the seasonal component and plot it.
plt.plot(classic_decomp.seasonal)
:::
Does the seasonality appear to be roughly centered around $0$? Is it regular, or does does the seasonal contribution itself change over time?
::::

Having obtained our estimates $\hat{T}_t$ and $\hat{S}_t$, the estimated residual $\hat{R}_t$ is simply what's left after subtracting the estimated trend and seasonality

$$
\begin{equation}
 	\hat{R}_t = x_t - \hat{T}_t - \hat{S}_t.
\end{equation}
$$

::::{tip} Problem
This exercise continues with the results of classical decomposition from the previous problems.

:::{code-cell} ipython3

# Get the residual component and plot it.
plt.plot(classic_decomp.resid)
:::
Does the residual appear to be roughly white noise centered around $0$? Can you see the effects of the COVID pandemic?
::::

### Multiplicative Methods

In certain scenarios, in particular with time series [exhibiting exponential growth](../chapter_4_enforcing_stationarity/05_log_transform.md#apple-mac-sales), a multiplicative decomposition may be more appropriate. While we can always apply additive decomposition to the logarithm of the original series, classical decomposition is capable of directly estimating the multiplicative decomposition (Eq. {eq}`mult-decomp`).

As with the additive case, we begin by obtaining our estimated trend $\hat{T}_t$ via a moving average. We detrend the series via *division*

$$
\begin{equation}
 	x_{t, detrend} = \frac{x_t}{\hat{T}_t}
\end{equation}
$$

We obtain our estimated seasonal component $\hat{S}_t$ by averaging each detrended season as before. For multiplicative decomposition the individual seasonal components are adjusted for a baseline of one, i.e.

$$
\prod_{t}\hat{S}_t \approx 1,
$$

For example the first quarter might have a seasonal component value of $125\%$ while the third quarter might have a component value of $80\%$. 

The residual is simply what remains after dividing out the estimated trend and seasonal effects

$$
\begin{equation}
	\hat{R}_t = \frac{x_t}{\hat{T}_t\hat{S}_t}.
\end{equation}
$$

### Plotting Decomposition Components

Above, we accessed the components of the classical decomposition and plotted them individually. `statsmodels` does have a nice feature to automatically plot the full decomposition invoked (using the variable names from above) as follows:

:::{code-cell} ipython3
classic_decomp.plot()
:::

This should provide you with a plot like

:::{figure} images/unemployment_classical_decomp.png
---
width: 95%
name: classic-decomp-unemployment
---
US employment rate from 1948 through 2025 from the [Federal Reserve Bank of St. Louis](https://fred.stlouisfed.org/series/UNRATENSA) with additive classical decomposition consisting of original series (top), trends and cycles (second from top), seasonal contribution (third from top), and residual variation not explained (bottom).
:::

:::{important} Magnitude of Contributions
By default, `statsmodels` will determine th y-axes scales individually for each component. Whenever comparing the contribution of different elements it is crucial to pay close attention to the y-axes in order to draw valid inference.
:::

## Assessing Decomposition Quality

A natural question to ask is how to assess the quality of a time series decomposition. Assuming that Eq. {eq}`additive-decomp` or Eq. {eq}`mult-decomp` is a reasonable model of the underlying data, a better decomposition can be expected to result in an estimated residual $\hat{R}_t$ far smaller than the trend and seasonal components. While visual inspection of a plot such as {fig}`classic-decomp-unemployment` or {fig}`stl-decomp-unemployment` (paying careful attention to the scale of the y-axes!) is a valuable starting point, in certain scenarios we may wish to have a more quantitative metric.

Hyndman et al. (@Hyndman_2026) recommend the [following formula](https://otexts.com/fpppy/nbs/04-features.html#sec-stlfeatures) for estimating the strength of the trend

$$
\begin{equation}
	F_T = \max\Bigg(0, 1-\frac{\mathbb{V}(\hat{R}_t)}{\mathbb{V}(\hat{T}_t + \hat{R}_t)}\Bigg),
\end{equation}
$$ (FT-def)

the logic being that for a time series with a strong trend component, the variance of the residual component alone should be much smaller than the variance of the combined trend and residual components. Thus, for data with a strong trend that has been well isolated by $\hat{T}_t$, $F_T$ will be close to $1$. $F_T$ will be close to $0$ for data with a minimal trend and/or a poorly isolated $\hat{T}_t$[^1].

[^1]: The quantity $\frac{\mathbb{V}(\hat{R}_t)}{\mathbb{V}(\hat{T}_t + \hat{R}_t)}$ could conceivably be greater than $1$ if $\hat{T}_t$ and $\hat{R}_t$ have a [strong negative covariance](../chapter_2_background_math/04_variance_covariance.md#variance-of-multiple-random-variables). Eq. {eq}`FT-def` ensures that $F_T\in[0,1]$ by use of the maximum of $0$ or the computed value.

Computing $F_T$ from the unemployment data above using the formula

:::{code-cell} ipython3
max(0, 1-(np.var(classic_decomp.resid)/np.var(classic_decomp.trend+classic_decomp.resid)))
:::

gives a value of $0.928$, indicating a strong trend component that has been well isolated by $\hat{T}_t$.

The strength of the seasonal component $F_S$ is computed in the same manner as $F_T$ using the equation

$$
\begin{equation}
	F_S = \max\Bigg(0, 1-\frac{\mathbb{V}(\hat{S}_t)}{\mathbb{V}(\hat{S}_t + \hat{R}_t)}\Bigg).
\end{equation}
$$ (FS-def)

Calculating $F_S$ using the code

:::{code-cell} ipython3
max(0, 1-(np.var(classic_decomp.seasonal)/np.var(classic_decomp.seasonal +classic_decomp.resid)))
:::

gives a value of $0.620$. This is still a relatively high value, but compared to $F_T$ indicates either (1) a weaker seasonal component (unlikely based on visual examination of the original data), or that (2) our decomposition has not done as good a job isolating the seasonal component, resulting in $\hat{S}_t$ having a weaker contribution to the estimated decomposition shown in {fig}`classic-decomp-unemployment`. 

## Drawbacks to Classical Decomposition

While not a terrible method, classical decomposition does have a number of drawbacks that make algorithms such as STL preferable. The major issues (highlighted in the derivations and exercises above) are as follows:

1. Use of a moving average removes the first and last $\frac{m}{2}$ ($m$ even) or $\frac{m-1}{2}$ ($m$ odd) observations from the trend (and consequently also the seasonal and residual components). For short time series this will result in sacrificing a fair amount of potential insight into the time series' behavior.
2. Averaging each season across the data assumes there is *exactly* one seasonal value per season that is constant across the entire time series, rather than allowing the seasonal contribution itself to be a function of time.
3. Classical decomposition is not robust to brief but extreme fluctuations such as the COVID pandemic and its effect on unemployment rates.

For these reasons, most statistics texts recommend against using the classical method for anything beyond a baseline to compare to more advanced methods. In the coming sections we will build on our analysis of classical decomposition to understand STL decomposition and how it addresses the issues above.