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
US employment rate from 1948 through 2025 from the [Federal Reserve Bank of St. Louis](https://fred.stlouisfed.org/series/UNRATENSA) (not seasonally adjusted).
:::

A time series with seasonal effects is not stationary because its mean is dependent on time. For example, {ref}`unemployment-raw` is not stationary because the expectation value for unemployment is consistently higher in January.

## Seasonal vs. Cyclic Changes

It is important to distinguish between *seasonal* effects and *cylic* effects. They both describe periodic fluctuations in a time series, but are distinguished by regularity.

:::{important} Cyclic Behavior and Stationarity
A time series exhibiting cycles can still be stationary as the irregular frequency of fluctuations means that no specific set of points has a higher expectation value than any other set.
:::

Seasonal and cyclic effects are also sometimes distinguished by cycle length, in particular in econometrics. Behavior with a period of up to a year is often binned as seasonal, whereas behavior with periods longer than a year or two is binned as cyclic. It is important to understand that this is a common characteristic, not a definition. **Nothing inherently precludes the existence of seasonal effects with periods far longer than a year or cyclic effects with periods far shorter than a year.**

:::{figure} images/ar2_sim.png
---
width: 95%
name: ar2-sim
---
Simulated time series showing strong cyclical behavior with irregular repetition frequency.
:::

{ref}`ar2-sim` shows a simulated time series with strong periodic behavior. However, since the periodicity is not constant, the underlying process is still stationary. In future chapters we will learn that this is a hallmark of autoregressive (AR) processes with complex roots.

::::{tip} Problem
We have observed that {ref}`unemployment-raw` exhibits seasonal effects with (potentially among others) yearly periodicity. Does it also exhibit cyclic behavior?
:::{dropdown} Click to reveal solution
**Solution:** Yes, in addition to a strong seasonality, {ref}`unemployment-raw` also exhibits cycles of increasing and decreasing unemployment rates with periodicities ranging from approximately $3-10$ years in duration.
:::
::::

### Distinguishing Seasonal and Cyclic Effects

So how do we know if a periodic effect is seasonal (and hence makes the time series non-stationary) or cyclic (and does not effect stationarity)? Some cycles such as {ref}`ar2-sim` or the multi-year cycles in {ref}`unemployment-raw` are fairly easy to identify as not being seasonal. On the other hand, how do we verify that effects such as the yearly cycles in unemployment are seasonal and not cyclic? While there is no foolproof way to demonstrate that an effect is seasonal, there are a few tools we can use to help:

1. **High regularity:** When we have a long time series such as {ref}`unemployment-raw` that covers over $75$ years, the extreme regularity of January spikes is almost certainly a seasonal effect. However, if we only had $10$ years of so to examine and/or had a much noisier time series, regularity would not be as strong an indicator.
2. **Domain expertise:** As with much of data science, we seek to incorporate priors from domain expertise. For example, it is quite reasonable to assume annual effects leading to an increase in unemployment in the same month every year. In contrast, had we seen an effect with, say, a seven month period, it would be less likely to have been a true seasonal effect.
3. **Autocorrelation:** The autocorrelation function for cyclic effects tends to display an exponential decay with sinusoidal behavior, such the autocorrelation function for {ref}`ar2-sim` shown in {ref}`ar2-sim-acf`:

    :::{figure} images/ar2_sim_acf.png
    ---
    width: 95%
    name: ar2-sim-acf
    ---
    Autocorrelation function for simulated cyclic time series.
    :::

    In contrast, the autocorrelation function for the first difference[^1] of {ref}`unemployment-raw` shows marked spikes with a twelve-month periodicity:

    :::{figure} images/us_unemployment_rate_differenced_acf.png
    ---
    width: 95%
    name: unemployment-diff-acf
    ---
    Autocorrelation function of change in US employment rate from 1948 through 2025 from the [Federal Reserve Bank of St. Louis](https://fred.stlouisfed.org/series/UNRATENSA) (not seasonally adjusted).
    :::

    Be aware that for most time series you're likely to encounter in real life, the autocorrelation function is likely to be far more ambiguous than the examples above.

[^1]: We use the first difference rather than the original series because [differencing removes any trend and random walk characteristic](03_difference.md), allowing us to focus exclusively on the seasonal aspects of the series.

::::{tip} Problem
In this problem you will examine an example of an autocorrelation function that may not be as easy to categorize as reflecting either seasonal or cyclic behavior. `statsmodels` has a preloaded time series dataset of historical sunspot activity, load it into a dataframe and plot the autocorrelation using the following code:

:::{code-cell} ipython3
import statsmodels.api as sm
from statsmodels.graphics.tsaplots import plot_acf

sunspots_df = sm.datasets.sunspots.load_pandas().data
sunspots_df.index = pd.Index(pd.date_range("1700", end="2009", freq="YE-DEC"))
sunspots_df.drop(columns="YEAR", inplace=True)
plot_acf(sunspots_df, lags=50)
:::
Does the plot of the autocorrelation function resemble either {ref}`ar2-sim-acf` or {ref}`unemployment-diff-acf`? What conclusions would you draw regarding the periodicity of sunspots?
::::

In subsequent chapters, we cover additional methods for identifying seasonality using time series decomposition and Fourier analysis.

## Methods for Addressing Seasonality

There are two major reasons we might want to remove seasonal effects from our time series:

1. Seasonal effects result in non-stationary time series, whereas methods such as the ARMA family require stationarity.
2. We may wish to identify long-term patterns and trends that can be obscured by normal seasonal fluctuations.

We will cover three major methods of removing seasonal effects.

### Moving Average

The first approach is arguably also the most intuitive, namely taking a *moving average* (also known as a *rolling average*) of our time series values with a window equal to the length of the "season." For example, if we are focused on daily temperatures but have hourly data, we might take a 24-hour moving average such that the first hourly value in our series is the mean of hours $0-23$, the second value is the mean of hours $1-24$, and so on.

::::{tip} Problem

Would using a 48-hour window remove daily seasonality? What about a 36-hour window?

:::{dropdown} Click to reveal solution
**Solution:** The key element is to have the same number of each season (hours of the day, in our example) for each averaged season. Every single 48-hour window will have two midnights, two 11 AMs, etc. Therefore, 24 or any integer multiple thereof will smooth out seasonal effects. In contrast, a 36-hour window beginning in the morning will have two days but only one night, while one starting in the evening will have two nights but only one day. Consequently, a 36-hour window—or any window that is not a multiple of 24—will not compensate correctly for seasonal effects.
:::
::::

:::{figure} images/unemployment_moving_average.png
---
width: 95%
name: unemployment-rolling
---
US employment rate from 1948 through 2025 from the [Federal Reserve Bank of St. Louis](https://fred.stlouisfed.org/series/UNRATENSA) with raw data (gray) and a centered 12-month moving average (blue).
:::
 
### Seasonal Adjustment

*Seasonal adjustment* is a central technique in econometrics and is used heavily by organizations such as the Federal Reserve Bank. We will differ a complete discussion of seasonal adjustment until next chapter; for now we may think of it as defining a set seasonal effect $S_t$ for each season and adjusting the time series to remove it. Seasonal adjustment is performed via one of two major methods:

1. **Additive:** By far the more common method, additive models assume our time series can be adjusted by subtracting $S_t$:
    $$
    y_t = x_t - S_t,
    $$
    where for example we might calculate that January is $20$ degrees colder than average and July is $25$ degrees hotter, giving
    $$
    \begin{align*}
    S_{January} &= -20\\
    &\ldots\\
    S_{July} &= 25\\
    &\ldots
    \end{align*}
    $$

2. **Multiplicitive:** Less commonly, we might instead assume our time series can be seasonally adjusted by dividing by $S_t$:
    $$
    y_t = \frac{x_t}{S_t}.
    $$
    Multiplicative seasonal adjustment most commonly applies in scenarios showing exponential growth such as stock or commodity prices without adjustment for inflation. Relative prices for gasoline might consistently be a few percentage points higher in the summer than the winter, but the absolute price difference in 2026 is likely to be substantially greater than the absolute difference in 1976.

:::{figure} images/unemployment_rate_seasonally_adjusted.png
---
width: 95%
name: unemployment-seasonally-adjusted
---
US employment rate from 1948 through 2025 from the [Federal Reserve Bank of St. Louis](https://fred.stlouisfed.org/series/UNRATENSA) after applying seasonal adjustment.
:::

### Seasonal Differencing