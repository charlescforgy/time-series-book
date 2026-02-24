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
US employment rate from the [Federal Reserve Bank of St. Louis](https://fred.stlouisfed.org/series/UNRATENSA) with STL decomposition consisting of original series (top), trends and cycles (second from top), seasonal contribution (third from top), and residual variation not explained (bottom).
:::