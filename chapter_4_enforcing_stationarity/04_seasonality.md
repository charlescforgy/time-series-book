# 4.4 Time Series with Seasonality

## Seasonality Definition

One of the signal characteristics of time series is the presence of *seasonality*. Put simply, seasonality is a **regular and predictable** variation in a time series. Some examples of seasonal effects are:

- Higher temperatures during the day than the night
- Higher temperatures in July than January (in the northern hemisphere)
- Unemployment rates increasing in January after the holiday season
- Higher traffic density on weekdays than weekends

Note that multiple seasonal effects of different time scales may be layered together, for example higher temperatures during the day combining with higher temperatures during the summer.

:::{figure} images/us_unemployment_rate.png
---
width: 95%
name: unemployment-raw
---
US employment rate from 1948 through 2025 [Federal Reserve Bank of St. Louis](https://fred.stlouisfed.org/series/UNRATENSA) (not seasonally adjusted).
:::

A time series with seasonal effects is not stationary because its mean is dependent on time. For example, {ref}`unemployment-raw` is not stationary because the expectation value for unemployment is consistently higher in January.

### Seasonal vs. Cyclic Changes

It is important to distinguish between *seasonal* effects and *cylic* effects. They both describe periodic fluctuations in a time series, but are distinguished by regularity.

:::{important} Cyclic Behavior and Stationarity
A time series exhibiting cycles can still be stationary as the irregular frequency of fluctuations means that no specific set of points has a higher expectation value than any other.
:::

Seasonal and cyclic effects are also sometimes distinguished by cycle length, in particular in econometrics. Behavior with a period of up to a year is often binned as seasonal, whereas behavior with periods longer than a year or two is binned as cyclic. It is important to understand that this is a common characteristic, not a definition. **Nothing inherently precludes the existence of seasonal effects with periods far greater than a year or cyclic effects with periods far shorter than a year.**

:::{figure} images/ar2_sim.png
---
width: 95%
name: ar2-sim
---
Simulated time series showing strong cyclical behavior with irregular repetition frequency.
:::

{ref}`ar2-sim` shows a simulated time series with strong periodic behavior. However, since the periodicity is not constant, the underlying process is still stationary. In future chapters we will see that this is a hallmark of autoregressive (AR) processes with complex roots.