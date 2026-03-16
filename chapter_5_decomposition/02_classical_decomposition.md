# 5.2 Classical Decomposition

## Components of Time Series

Many time series contain of a [trend](../chapter_4_enforcing_stationarity/02_trend.md#definition-of-trend), [seasonal effects](../chapter_4_enforcing_stationarity/04_seasonality.md#seasonality-definition), and/or [cycles](../chapter_4_enforcing_stationarity/04_seasonality.md#seasonal-vs-cyclic-changes). *Time series decomposition*, or simply *decomposition*, is defined as methods to isolate each component's contribution to an overall time series. Since cycles lack a specific length it is difficult to draw a clear distinction between an upward trend followed by a downward trend and a cycle. Consequently, we will generally fold cycles into the trend for the purposes of decomposition.

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
which is more applicable, for example, in forecasting quarterly sales of a company experiencing compound growth such as [Mac sales previous chapter](../chapter_4_enforcing_stationarity/05_log_transform.md#apple-mac-sales). Note that we can always recast Eq. {eq}`mult-decomp` into the form of Eq. {eq}`additive-decomp` by using a log transform

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

Recall that a moving average with [the length of a season (or any multiple thereof)](../chapter_4_enforcing_stationarity/04_seasonality.md#moving-average) will remove seasonal effects, allowing us to focus on longer term cycles and trends. For seasonal effects with odd numbers of time steps per season such as a $m=7$ for weekly seasonality, we employ a simple centered moving average of length $m$. Thus, for data starting on a Sunday, the first Wednesday will be replaced with the average of the first Sunday-Saturday, the first Thursday will be replaced by the average of the the first Monday-Sunday, and so on. Note the we do loose the first and last $\frac{m-1}{2}$ observations. This is not an issue for long time series in which the number of observations $n>>m$, but does pose a challenge for shorter time series.

:::{table} $7-$Day Moving Average

:name: weekly-moving-average

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
$$

:::{table} Quarterly Moving Average

:name: quarterly-moving-average

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

From here on, classical decomposition subdivides into additive and multiplicative methods.

### Additive Method

### Multiplicative Methods