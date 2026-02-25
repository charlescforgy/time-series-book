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


Note that while other models are possible, for example

$$
x_t = S_t\times T_t + R_t,
$$

such models are almost never used in practice.