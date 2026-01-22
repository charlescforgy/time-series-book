# 3.5 Cross-Covariance and Cross-Correlation

## Cross-Covariance Definition

When examining the relationship between two time series, it is often useful to see how they move in tandem. One powerful tool for this is the *cross-correlation function* defined as

$$
\begin{equation}
\gamma_{x,y}(s,t) \stackrel{\triangle}{=}\mathbb{E}\big[(x_s-\mu_{x,s})(y_t-\mu_{y,t})\big]
\end{equation}
$$ (ccovf-non-stationary-def)

for time series $x_s$ and $y_t$. Analogous to stationarity for autocovariance, the two series are said to be *jointly stationary* if both:
1. $x_s$ and $y_t$ are individually stationary.
2. The cross-covariance is only a function of the lag $h=s-t$. 

In such a case, Eq. {eq}`ccovf-non-stationary-def` can be simplified to 

$$
\begin{equation}
\gamma_{x,y}(h) =\mathbb{E}\big[(x_{t+h}-\mu_{x})(y_t-\mu_{y})\big].
\end{equation}
$$ (ccovf-stationary-def)

Since we are dealing with two distinct time series, it is generally the case that $\gamma_{x,y}(h)\neq\gamma_{x,y}(-h)$ for $h\neq0$. This is due to the fact that $\gamma_{x,y}(h)$ represents the covariance between $y$ and $x$ at $h$ lags in the *future*, whereas $\gamma_{x,y}(-h)$ represents the covariance between $y$ and $x$ at $h$ lags in the *past*. Put differently, there is no reason to assume the covariance between employment and the following month's inflation is the same as the covariance between employment and the preceding month's inflation.

::::{tip} Problem
Why have we dropped the subscripts $s$ and $t$ from $\mu$ in Eq. {eq}`ccovf-stationary-def`?

:::{dropdown} Click to reveal solution
**Solution:** By assumption, both $x_s$ and $y_t$ are individually stationary. As such, the means $\mu_x$ and $\mu_y$ are independent of time, making the subscript for time superfluous.
:::
::::

## Cross-Correlation Definition

The cross-correlation function is defined as

$$
\rho_{x,y}(s,t) \stackrel{\triangle}{=} \frac{\gamma_{x,y}(s,t)}{\sqrt{\gamma_x(s,s)\gamma_y(t,t)}}.
$$ (ccorf-non-stationary-def)

For jointly stationary time series, Eq. {eq}`ccorf-non-stationary-def` simplifies to

$$
\rho_{x,y}(h) = \frac{\gamma_{x,y}(h)}{\sqrt{\gamma_x(0)\gamma_y(0)}}.
$$ (ccorf-stationary-def)

::::{tip} Problem
We have seen that for any finite variance process $x_t$, $\rho_x(0)=1$. Is $\rho_{x,y}(0)$ also always $1$? Why or why not?

:::{dropdown} Click to reveal solution
**Solution:** No, it need not always be $1$. $\rho_x(0)$ represents a pure *variance* divided by itself, resulting in $1$. In contrast, even at lag $0$, $\rho_{x,y}(0)$ is still the *covariance* of two random variables divided by their standard deviations. Consequently, $\rho_{x,y}(0)$ can fall anywhere in $[-1,1]$ (by the Cauchy-Schwarz inequality).
:::
::::

```{note} Leading vs. Lagging
For $\gamma_{x,y}(h)$ or $\rho_{x,y}(h)$, when $h>0$ we say $y_t$ is a *leading indicator* for $x_t$ (or "leads" $x_t$) because $y_t$ allows us to predict what will happen to $x_t$ in the future. Equivalently, especially in scenarios in which $y_t$ is difficult to directly observe, we can say that $x_t$ is a *lagging indicator* for $y_t$ (or "lags" $y_t$) because it allows us to infer past values of $y_t$. Similarly, when $h<0$ we say $x_t$ is a leading indicator (or $y_t$ is a lagging indicator).
```


## Cross-Covariance and Cross-Correlation in `statsmodels`

In `statsmodels`, the cross-covariance and cross-correlation are accessed by `statsmodels.tsa.stattools.ccovf` and `statsmodels.tsa.stattools.ccf`, respectively. The only arguments we need worry about are `x` and `y`, the two time series. Note that these functions only calculate positive $h$ values, to get both positive and negative values (i.e. both `x` and `y` leading) you must run both `ccf(x,y)` and `ccf(y,x)`.

```{warning} Stationarity of Series
`statsmodels` uses the forms from Eq.s {eq}`ccovf-stationary-def` and {eq}`ccorf-stationary-def`, which rely on the assumptions of stationarity. If you attempt to run them on non-stationary time series you may get results such as correlations with an absolute value greater that $1$. We will cover tools for making a non-stationary time series stationary in the next chapter.
```

```{figure} images/notional_ccf.png
---
width: 95%
name: notional-ccf
---
Notional plot of cross-correlation function for two arbitrary time series consisting of phase shifted sine curves with noise. Note that in `statsmodels` it is necessary to calculate positive and negative $h$ values separately.
```