# 4.3 Differencing and the Backshift Operator

## Random Walk

As discussed, a random walk [has a non-constant variance](../chapter_3_autocovariance/02_autocovariance.md#mean-and-autocovariance-of-random-walk). Since $\gamma(0)$ is not constant, a random walk (even without drift) is not stationary. How might we go about isolating a stationary time series from a random walk? Detrending will remove any drift thereby stabilizing the mean, but it will not help with the non-constant variance. Instead, we rely on the process of *differencing*.

Differencing is defined as taking the difference between values. For example, the first difference of a random walk without drift is given by

$$
z_t = x_{t} - x_{t-1}.
$$

Note that (starting our time series with $x_0=0$)

$$
\begin{aligned}
z_1 &= x_1 - x_0\\ 
&= (x_0+w_1) - 0\\
&= w_1\\
z_2 &= x_2-x_1\\
&= (x_1+w_2) - x_1\\
&= w_2\\
&\ldots
\end{aligned}
$$ (random-walk-no-drift-diff)

Thus, the first difference of a random walk without drift is white noise and hence stationary. What about a random walk with drift? Starting this time with $x_0=\delta$

$$
\begin{aligned}
z_1 &= x_1 - x_0\\ 
&= (\delta + x_0+w_1) - \delta\\
&= (2\,\delta +w_1) - \delta\\
&= \delta + w_1\\
z_2 &= x_2-x_1\\
&= (\delta + x_1+w_2) - x_1\\
&= \delta + w_2\\
&\ldots
\end{aligned}
$$ (random-walk-drift-diff)

Since $\delta$ is constant, $\mathbb{E}[z_t]=\delta$ is also constant. Similarly, the addition of $\delta$ [does not change the covariance $\gamma(h)$](../chapter_3_autocovariance/02_autocovariance.md#random-walk-variance), so the first difference of a random walk with drift is also stationary.

:::{note} Forecasting Random Walk
For a pure random walk, it is impossible to make a better statistical prediction that predicting that the next value will be the same as the current value plus any drift when applicable.
:::

## Difference Stationary Process

For pure random walk processes, Eq.s {eq}`random-walk-no-drift-diff` and {eq}`random-walk-drift-diff` represent the limit of our analysis. However, very often we will encounter a *difference stationary process* defined again as

$$
\begin{equation}
x_t = \mu_t + y_t
\end{equation}
$$ (difference-stationary-def)

where in this case $\mu_t$ is a random walk process[^1]. Taking the first difference, we have

[^1]: We will discover other scenarios of difference stationary process where $\mu_t$ is not a simple random walk, but is a process possessing a *unit root*. We will defer discussion of these cases until after we have covered ARMA processes. For the time being, you can think of the concept of unit root as referring to random walks.

$$
\begin{aligned}
x_t-x_{t-1} &= (\mu_t+y_t) - (\mu_{t-1}+y_{t-1})\\
&= (\delta +\mu_{t-1} + w_t+y_t) - (\mu_{t-1} + y_{t-1})\\
&= \delta + y_t - y_{t-1} + w_t
\end{aligned}
$$

If $y_t$ is stationary, the first difference $v_t=y_t-y_{t-1}$ must be as well:

$$
\mathbb{E}[v_t] = \mathbb{E}[y_t-y_{t-1}]=\mathbb{E}[y_t]-\mathbb{E}[y_{t-1}]=\mu_y-\mu_y=0
$$

and

$$
\begin{aligned}
\gamma_v(h) &= \text{Cov}(v_{t+h}, v_t)\\
&= \text{Cov}(y_{t+h}-y_{t+h-1}, y_t-y_{t-1})\\
&= 2\,\gamma_y(h) - \gamma_y(h-1)-\gamma_y(h+1)
\end{aligned}
$$

::::{tip} Problem
Prove that the fact that $v_t$ is stationary demonstrates that $z_t=x_t-x_{t-1}$ must be stationary as well.

:::{dropdown} Click to reveal solution
**Solution:** We have already show that $\mathbb{E}[y_t-y_{t-1}]=0$. Consequently,

$$
\mathbb{E}[x_t-x_{t-1}] = \mathbb{E}[\delta + y_t - y_{t-1} + w_t] = \delta
$$

which is clearly constant for all $t$. Similarly, (recognizing that the constant $\delta$ has no effect on the covariance) $\gamma_z(h)$ is given by

$$
\begin{aligned}
&\text{Cov}(y_{t+h} - y_{t+h-1} + w_{t+h}, y_t - y_{t-1} + w_t)\\
&=\text{Cov}(y_{t+h} - y_{t+h-1}, y_t - y_{t-1})+\text{Cov}(w_{t+h}, w_t)\\
&= 2\,\gamma_y(h) - \gamma_y(h-1)-\gamma_y(h+1) + \gamma_w(h)\\
&= 2\,\gamma_y(h) - \gamma_y(h-1)-\gamma_y(h+1) + \delta_{h,0}\sigma_w^2
\end{aligned}
$$

where we have assumed iid $w_t$.
:::
::::

## Differencing to Differentiation

### Finite Differences

Differencing is closely related to the discrete analog to differentiation. Assuming our time series $p_t$ originates from the continuous process $p(t)$ with first derivative $\frac{d}{dt}p(t)$, the backward finite difference approximation to the derivative is

$$
\begin{equation}
	\frac{d}{dt}p(t) \approx \frac{p_t-p_{t-1}}{\Delta t}.
\end{equation}
$$ (first-finite-diff)

As we are assuming a constant sampling rate, we can treat $\Delta t$ as $1$ in the unit of our time steps (seconds, days, years, etc.). By the same logic, we can approximate the second derivative using the backward second-order difference

$$
\begin{aligned}
	\frac{d^2}{dt^2}p(t) &\approx \frac{\frac{p_{t}-p_{t-1}}{\Delta t}-\frac{p_{t-1}-p_{t-2}}{\Delta t}}{\Delta t}\\
	&=\frac{p_{t}-2p_{t-1}+p_{t-2}}{(\Delta t)^2}.
\end{aligned}
$$ (second-finite-diff)

Where as before we can reduce to $p_{t}-2p_{t-1}+p_{t-2}$ by treating $\Delta t$ as $1$[^2].

[^2]: Note that treating $\Delta t$ as 1 will cause the values to agree, but the units will not be the same. Eq. {eq}`first-finite-diff` includes units of $\text{time}^{-1}$ and Eq. {eq}`second-finite-diff` includes units of $\text{time}^{-2}$.

### Differencing Notation

Because differencing plays such a central role in time series analysis, there are specific notations designed to allow easier manipulation. The first difference is denoted by $\nabla$

$$
\begin{equation}
	\nabla x_t \stackrel{\triangle}{=}x_t-x_{t-1}.
\end{equation}
$$ (diff-def)

Note the similarity to Eq. {eq}`first-finite-diff`. The second difference is denoted by $\nabla^2$ and resembles Eq. {eq}`second-finite-diff`

$$
\begin{aligned}
	\nabla^2 x_t &\stackrel{\triangle}{=} \nabla x_t - \nabla x_{t-1}\\
	&= (x_t -x_{t-1}) - (x_{t-1}-x_{t-2})\\
	&= x_t - 2x_{t-1} + x_{t-2}.\\
\end{aligned}
$$

Higher order differences can be defined analogously, but in practice we will almost never need to go beyond the second difference.

### Backshift Operator

The *backshift operator*, $\mathbb{B}$, is a valuable method in time series analysis. While at first it may seem like we are introducing notation for its own sake, over the course of the semester we will see that the backshift operator is an elegant and powerful way to manipulate time series.

:::{note} Lag vs. Backshift Terminology
Some texts use the term *lag operator* instead of backshift operator. `statsmodels` uses this term instead, which is denoted by $L$. The underlying principle is identical.
:::

The backshift operator changes a member of a series to the preceding value, i.e.:

$$
\begin{equation}
	\mathbb{B} x_t = x_{t-1}
\end{equation}
$$ (backshift-def)

Similarly, $\mathbb{B}^2x_t=\mathbb{B}(\mathbb{B}x_t)=\mathbb{B}(x_{t-1})=x_{t-2}$, and so on.

For completeness, we also define $\mathbb{B}$'s inverse $\mathbb{B}^{-1}$ as the forward-shift operator such that $\mathbb{B}\mathbb{B}^{-1}=\mathbb{B}^{-1}\mathbb{B}=1$.

### Differencing in Backshift Operator Notation

Combining Eq. {eq}`diff-def` and Eq. {eq}`backshift-def`, we can rewrite the first difference with unit time as

$$
\begin{aligned}
	\nabla x_t &= x_t - x_{t-1}\\
	&= x_t - \mathbb{B} x_t\\
	&= (1-\mathbb{B})x_t.
\end{aligned}
$$

The second difference can be expressed as

$$
\begin{aligned}
	\nabla^2 x_t &= (1-\mathbb{B})^2x_t\\
					&= (1-2\mathbb{B}+\mathbb{B}^2)x_t\\
					&= x_t -2x_{t-1} + x_{t-2}.
\end{aligned}
$$

Higher order differences $d$ are defined as $(1-\mathbb{B})^d$.

::::{tip} Problem
Find $(1-\mathbb{B}^{-1})^d$ for $d=1,2$.

:::{dropdown} Click to reveal solution
**Solution:**
This is most easily demonstrated by using a trial series $x_t$:

$$
(1-\mathbb{B}^{-1})x_t = x_t - x_{t+1}\\
$$

For our purposes the forward and backward finite differences are identical because they only differ from one another by the index choice. Thus we can freely reindex with $s=t+1$:

$$
\begin{aligned}
x_t - x_{t+1}&=x_{s-1} - x_{s}\\
&=-(x_s - x_{s-1})\\
&=-(1-\mathbb{B})x_s
\end{aligned}
$$

Thus $(1-\mathbb{B}^{-1})=-(1-\mathbb{B})$.

While this suggests that $(1-\mathbb{B}^{-1})^2=(-(1-\mathbb{B}))^2=(1-\mathbb{B})^2$, let's see if we can derive it explicitly. A similar manipulation provides:

$$
\begin{aligned}
(1-\mathbb{B}^{-1})^2 x_t &= (1-2\mathbb{B}^{-1}+\mathbb{B}^{-2})x_t\\
&=x_t-2x_{t+1}+x_{t+2}\\
&=x_{t+2}-2x_{t+1}+x_{t}
\end{aligned}
$$

reindexing with $v=t+2$:

$$
\begin{aligned}
x_{t+2}-2x_{t+1}+x_{t}&=x_v - 2x_{v-1} + x_{v-2}\\
&=(1 - 2\mathbb{B} + \mathbb{B}^2) x_v\\
&=(1-\mathbb{B})^2 x_v
\end{aligned}
$$

Thus $(1-\mathbb{B}^{-1})^2$ is effectively the same as $(1-\mathbb{B})^2$.
:::
::::

## Differencing vs. Detrending

While both detrending and differencing have their places, differencing is more commonly favored. Differencing has the major advantage that it is non-parametric, i.e. it does not rely on assuming any model (beyond a random walk) and parameters. In contrast, detrending assumes the existence of a linear (or higher-order) trend. Moreover, differencing a stationary series, while undesirable[^3], will result in another stationary series, whereas detrending a stationary series will introduce the opposite trend.

[^3]: When we cover ARMA processes we will see that unnecessary differencing adds an additional moving average (MA) term and frequently results in non-invertible models.

Differencing more naturally extends to higher derivatives, for example using the second difference for constant acceleration processes. Detrending via quadratic fit lines makes very strong assumptions about the underlying model and is prone to overfitting.

In contrast, detrending provides a readily interpretable model of the overall trend that can be communicated to clients or sponsors. Detrending allows a clean explanation along the lines of "After removing a steady three unit per month increase, we see that...", which is not possible with differencing. While differencing is more commonly the favored approach, ultimately the choice depends on your use case and intended audience.

### Differencing S&P 500

Previously, [we detrended the S&P 500 using a linear trend](02_trend.md#trends-and-detrending), resulting in the plot:

:::{figure} images/sp_500_detrended_values.png
---
width: 95%
name: sp-500-detrended
---
Detrended values of S&P 500 index for the 10-year period from January 2016 through January 2026 from [Federal Reserve Bank of St. Louis](https://fred.stlouisfed.org/series/SP500) detrended using $\text{SP500}_{detrended} = \text{SP500} - 1645 - 1.66\,t$.
:::

What would happen if we instead take the first difference? `pandas` has a `diff` method accessed by `df.diff(periods=1)`. Running the code

:::{code-cell} ipython3
sp_500_diff = sp_500_df.diff().dropna()
:::

we can used the differenced time series to create {ref}`sp-500-differenced`

:::{figure} images/sp_500_differenced.png
---
width: 95%
name: sp-500-differenced
---
First difference of values of S&P 500 index for the 10-year period from January 2016 through January 2026 from [Federal Reserve Bank of St. Louis](https://fred.stlouisfed.org/series/SP500).
:::

{ref}`sp-500-differenced` appears more likely to be stationary than {ref}`sp-500-detrended`, though I would caution [against relying too heavily on pure visual inspection](../chapter_1_introduction/04_basic_models.md#apparent-trends) for either trends or overall stationarity. The mean of the differenced S&P 500 is $1.93$, fairly close to the linear regression slope of $1.66$. {ref}`sp-500-differenced` does appear to exhibit *volatility clustering*, which we will learn in subsequent chapters can be understood via the ARCH family of models. Nevertheless, it is reasonable to conclude that the S&P 500 roughly follows a random walk with drift of $\delta\approx1.9$, making its first difference stationary white noise.

::::{tip} Problem
What would happen if you instead took the second difference? Run the code

:::{code-cell} ipython3
sp_500_second_diff = sp_500_df.diff().diff().dropna()
:::

and plot the results. How does your figure compare to {ref}`sp-500-differenced`?
::::

## Autocorrelation of Random Walk

Before examining the autocorrelation, let's work out what we expect the autocovariance of a random walk to look like. We've [established that the autocovariance of a random walk](../chapter_3_autocovariance/02_autocovariance.md#mean-and-autocovariance-of-random-walk) is given by

$$
\begin{aligned}
\gamma(s,t) &= \text{Cov}\Big(\sum_{i=0}^s w_i, \sum_{j=0}^t w_j\Big)\\
&= \sum_{i=0}^{\text{min}(s,t)} \mathbb{V}(w_i)\\
&= \text{min}(s,t)\,\sigma_w^2.
\end{aligned}
$$ (autocovariance-random-walk-min)

Translating to autocorrelation is a bit tricky as [a random walk's variance is non-constant](../chapter_3_autocovariance/02_autocovariance.md#mean-and-autocovariance-of-random-walk). Standard statistical packages still use [the sample approximation for $\gamma(h)$ and $\rho(h)$](../chapter_3_autocovariance/06_estimation.md#sample-autocorrelation)

$$
\begin{equation}
\hat{\rho}(h) = \frac{\hat{\gamma}(h)}{\hat{\gamma}(0)}.
\end{equation}
$$ (sample-acf)

Even though Eq. {eq}`sample-acf` is only strictly valid for stationary time series, we can use it to approximate the autocorrelation for our case as well. Combining Eq.s {eq}`autocovariance-random-walk-min` and {eq}`sample-acf` for a time series of length $t$ gives us

$$
\begin{aligned}
\hat{\rho}(h) &\approx \frac{\hat{\gamma}(s,t)}{\hat{\gamma}(t,t)}\\
&= \frac{\text{min}(s,t)\,\sigma_w^2}{t\,\sigma_w^2}\\
&= \frac{\text{min}(s,t)}{t},
\end{aligned}
$$

which represents a linear decay as $h=t-s$ increases.

:::{important} Random Walk Autocorrelation
The sample autocorrelation of a random walk decays approximately linearly with respect to $h$.
:::

::::{tip} Problem
What would the autocovariance of the first difference of a random walk look like?

:::{dropdown} Click to reveal solution
**Solution:** As demonstrated in Eq.s {eq}`random-walk-no-drift-diff` and {eq}`random-walk-drift-diff`, the first difference of a random walk is simply white noise with the addition of a drift term when applicable. iid white noise has an autocovariance of $\gamma(h)=\delta_{h,0}\sigma_w^2$. The autocovariance $\gamma(h)$, and consequently the autocorrelation $\rho(h)$, will be $0$ for all $h>0$.
:::
::::

Do the autocorrelation functions agree with our assessment that the S&P 500 is a random walk? Let's look at them:

:::{figure} images/sp_500_raw_acf.png
---
width: 95%
name: sp-500-raw-acf
---
Autocorrelation function of S&P 500 returns from [Federal Reserve Bank of St. Louis](https://fred.stlouisfed.org/series/SP500).
:::

{ref}`sp-500-raw-acf` certainly looks like pure linear decay. What about the autocorrelation of the differenced series?

:::{figure} images/sp_500_differenced_acf.png
---
width: 95%
name: sp-500-differenced-acf
---
Autocorrelation function of first difference of S&P 500 returns from [Federal Reserve Bank of St. Louis](https://fred.stlouisfed.org/series/SP500).
:::

Multiple $h$ values before $h\approx15$ are significant beyond [the level expected for pure white noise](../chapter_3_autocovariance/06_estimation.md#standard-error-of-autocorrelation-and-cross-correlation), indicating that our original process was probably not a pure random walk. Nevertheless, the autocorrelation values are small enough to say that a random walk is a reasonable first approximation to the S&P 500 returns.

:::{tip} Problem
Plot the autocorrelation function of the detrended values from [the previous section](02_trend.md#detrending-sp-500). Does the plot resemble {ref}`sp-500-raw-acf`, {ref}`sp-500-differenced-acf`, or is it distinct from both?
:::