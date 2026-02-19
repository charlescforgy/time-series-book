# 4.4 Time Series with Seasonality

## Seasonality Definition

One of the defining characteristics of time series is the presence of *seasonality*. Put simply, seasonality is a **regular and predictable** variation in a time series. Some examples of seasonal effects are:

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

It is important to distinguish between *seasonal* effects and *cyclic* effects. They both describe periodic fluctuations in a time series, but are distinguished by regularity.

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

So how do we know if a periodic effect is seasonal (and hence makes the time series non-stationary) or cyclic (and does not affect stationarity)? Some cycles such as {ref}`ar2-sim` or the multi-year cycles in {ref}`unemployment-raw` are fairly easy to identify as not being seasonal. On the other hand, how do we verify that effects such as the yearly cycles in unemployment are seasonal and not cyclic? While there is no foolproof way to demonstrate that an effect is seasonal, there are a few tools we can use to help:

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

*Seasonal adjustment* is a central technique in econometrics and is used heavily by organizations such as the Federal Reserve Bank. We will defer a complete discussion of seasonal adjustment until next chapter; for now we may think of it as defining a set seasonal effect $S_t$ for each season and adjusting the time series to remove it. Seasonal adjustment is performed via one of two major methods:

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
    Multiplicative seasonal adjustment most commonly applies in scenarios showing exponential growth such as stock or commodity prices (without adjustment for inflation). Relative prices for gasoline might consistently be a few percentage points higher in the summer than the winter, but the absolute price difference in 2026 is likely to be substantially greater than the absolute difference in 1976.

:::{figure} images/unemployment_rate_seasonally_adjusted.png
---
width: 95%
name: unemployment-seasonally-adjusted
---
US employment rate from 1948 through 2025 from the [Federal Reserve Bank of St. Louis](https://fred.stlouisfed.org/series/UNRATENSA) using Federal Reserve Bank's seasonal adjustment method.
:::

::::{tip} Problem

Several important seasonal adjustment algorithms only use additive adjustments. How might we apply such methods to a time series that exhibits exponential growth in seasonal fluctuations?

:::{dropdown} Click to reveal solution
**Solution:** We can always convert division to subtraction by taking the logarithm. If the initial time series obeys 
$$
y_t = \frac{x_t}{S_t}
$$
we can define a new time series $z_t\overset{\triangle}{=}\log{(y_t)}$ which now follows the relation
$$
z_t = \log{(x_t)} - \log{(S_t)}.
$$
:::
::::

### Seasonal Differencing

The final method we will use is *seasonal differencing*. We have seen how [the first difference removes random walk characteristics](03_difference.md#random-walk). By the same token, we may build a model consisting of a *seasonal* random walk in which the current season's value is determined by last season's value plus some noise $w_t$. For example, we might propose a monthly model

$$
\begin{align*}
x_{January,\, t} &= x_{January,\, t-1} +w_{January,\, t}\\
x_{February,\, t} &= x_{February,\, t-1} +w_{February,\, t}\\
&\vdots
\end{align*}
$$ (seasonal-random-walk)

where $t$ refers to the current year and $t-1$ to the previous year. Allowing that Eq. {eq}`seasonal-random-walk` is a valid representation of the underlying process generating our time series, the natural method to compensate for its effects is to take the *seasonal difference* $\nabla_s$

$$
\begin{equation}
\begin{split}
z_t &\overset{\triangle}{=} \nabla_s\,x_t\\
&= x_{t}-x_{t-s}\\
&= (1-\mathbb{B}^{s})\,x_t
\end{split}
\end{equation}
$$

where $s$ is the number of seasons per period; in the case of monthly data we have $s=12$ giving us $\nabla_{12}$. 

:::{important} $\nabla_s$ vs. $\nabla^s$

$\nabla_s$ is the first difference at separation $s$ and equals $1-\mathbb{B}^{s}$, while $\nabla^s$ is the $s\text{th}$ difference at separation $1$ and equals $(1-\mathbb{B})^{s}$.
:::

:::{figure} images/unemployment_12_month_change.png
---
width: 95%
name: unemployment-12-month-difference
---
US employment rate from 1948 through 2025 from the [Federal Reserve Bank of St. Louis](https://fred.stlouisfed.org/series/UNRATENSA) after applying 12-month differencing.
:::

{ref}`unemployment-12-month-difference` shows the 12-month change in the US unemployment rate. While it still demonstrates cycles varying from around five to ten years in duration, these [do not negate stationarity](#seasonal-vs-cyclic-changes). {ref}`unemployment-12-month-difference-acf` shows the autocorrelation function for the series in {ref}`unemployment-12-month-difference`. The sinusoidally decaying nature of the autocorrelation is consistent with a [stationary cyclic time series](#distinguishing-seasonal-and-cyclic-effects).

:::{figure} images/unemployment_12_month_change_acf.png
---
width: 95%
name: unemployment-12-month-difference-acf
---
Autocorrelation function of US employment rate from 1948 through 2025 from the [Federal Reserve Bank of St. Louis](https://fred.stlouisfed.org/series/UNRATENSA) after applying 12-month differencing.
:::

### Seasonal Adjustment vs. Seasonal Differencing

As with [differencing versus detrending](03_difference.md#differencing-vs-detrending), seasonal adjustment and seasonal differencing both have valid use cases. While there is no objectively superior method, there are some considerations that might push you toward one over the other.

**Arguments for seasonal adjustment:**

1. Seasonal adjustment maintains the overall underlying value of a time series (compare {ref}`unemployment-seasonally-adjusted` and {ref}`unemployment-12-month-difference`), making it useful for understanding trends and cycles.
2. Seasonally adjusted plots are generally easier to interpret than plots of differenced time series, especially for an audience with less experience in data science.
3. More sophisticated seasonal adjustment methods allow variations in the seasonal pattern, unlike seasonal differencing which assumes a single seasonal pattern across an entire time series.
4. Similarly, more sophisticated seasonal adjustment methods can maintain the length of the original time series, whereas differencing shortens the sequence by $s$ observations.

**Arguments for seasonal differencing:**

1. Differencing is less parametric than seasonal adjustment. Even if a given time series cannot be seasonally adjusted with adequate accuracy, seasonal differencing can still yield valid results.
2. Seasonal differencing is more likely to result in stationary time series than seasonal adjustment.
3. As a result of its better ability to create a stationary time series, seasonal differencing is the default method using in the *Seasonal ARIMA,* or SARIMA, family of models (though methods to use ARIMA with seasonal adjustment do exist).
4. Incorrectly applying seasonal differencing will not ruin a time series' stationary nature, whereas incorrectly applying seasonal adjustment to a stationary time series can backfire and introduce spurious seasonal effects.

:::{note} Explanatory vs. Predictive Data Science

While not an absolute rule, seasonal adjustment is generally preferred in explanatory data science where our primary goal is to understand overall historical behavior. In contrast, seasonal differencing is preferred when performing analysis and forecasting as it tends to be a more reliable method of ensuring stationarity.
:::

:::{tip} Problem

Find a few publicly reported time series such as those available from the Federal Reserve Bank of St. Louis. Do they use seasonal adjustment, seasonal differencing, or neither? What do you think motivates the choice?
:::