# Stationarity

## Strict vs. Weak Stationarity

One of the most central concepts in time series analysis is *stationarity*.  Loosely, a stationary time series is one that does not demonstrate any trend or seasonal variation. More concretely, there are two distinct definitions of stationarity:
1. *Strict stationarity* is defined as a time series for which the probability distribution of any subset of observations is identical, i.e. 
$$
P_{x}(x_{t_1}, x_{t_2},...,x_{t_k}) = P_{x}(x_{t_1+h}, x_{t_2+h},...,x_{t_k+h}).
$$

2. *Weak stationarity* is defined as a time series for which the following hold:

	i. The mean $E[x_t]$ is finite and constant across all times $t$.
    
    ii. The variance $\mathbb{V}(x_t)=\gamma(t, t)$ is finite and constant across all times $t$.
    
    iii. The autocovariance is a function of lag alone and not of absolute time, $\gamma(s, t)= \gamma(s+v, t+v)=\gamma(|s-t|)$.

An example of a strictly stationary series would be pure white noise. Stock returns (*not* values) are also reasonably approximated as being strictly stationary. Weakly stationary processes are far more prevalent; in coming chapters we will encounter numerous examples from fields such as medicine and econometrics.

### Strict to Weak Stationarity

It should be obvious that demonstrating weak stationarity does not demonstrate strict stationarity. Interesting, the converse also does not necessarily hold. Consider the white noise process defined by the standard Cauchy distribution:

$$
x_t \sim \text{Cauchy}(0,1)
$$

$x_t$ is strictly stationary as every point is drawn from the same probability distribution as every other point. However, $x_t$ is *not* weakly stationary as the Cauchy distribution lacks a finite variance. That said, any strongly stationary finite variance process is also weakly stationary.

## Advantages of Weak Stationarity

In practice, we generally do not use strict stationarity for two major reasons:

1. Establishing strict stationarity requires knowing the underlying probability distribution (or at least having a confident guess to it). In contrast, weak stationarity is established solely via observables in the form of the mean, variance, and autocovariance.
2. Strict stationarity is generally too strict for most purposes. We will see in coming chapters that methods requiring stationarity can be used with weakly stationary time series.

```{note}
Following sources such as [](https://doi.org/10.1007/978-3-031-70584-7) and [](https://doi.org/10.1007/978-1-4419-0320-4), we will use the "stationary" to refer to weak stationarity and specify "strict stationarity" when necessary.
```

## Autocovariance of a Stationary Process

As discussed [above](#stationarity), the autocovariance $\gamma(s,t)$ of a stationary process depends exclusively on the lag $h=|s-t|$. Consequently, when dealing with stationary processes we will simply use the notation

$$
\gamma(h), \qquad h=|s-t|
$$

where $\gamma(0)$ is the variance, $\gamma(1)$ is the autocovariance at lag $1$, and so on. Note that 

$$
\gamma(-h) = \gamma(|t-s|) =  \gamma(|s-t|) = \gamma(h),
$$

consequently, we will only calculate $\gamma(h)$ for $h\geq0$.

**Problem:** What can we say about the relationship between $\gamma(h)$ and $\gamma(0)$ given that the relevant time series is stationary?

```{dropdown} Click to reveal solution
**Solution:** As demonstrated in [Chapter 2](../chapter_2_background_math/04_variance_covariance.md#geometric-inequality), for any finite variance random variables $X$ and $Y$

$$
|\text{Cov}(X, Y)| \leq \sqrt{\mathbb{V}(X)\mathbb{V}(Y)}.
$$

As $\gamma(s,s) = \gamma(t,t)=\gamma(0)$ and $\gamma(s,t)=\gamma(|s-t|)=\gamma(h)$, we conclude

$$
|\gamma(h)| \leq \gamma(0).
$$

This is turn guarantees that the value $\frac{\gamma(h)}{\gamma(0)}$ lies in the interval $[-1,1]$.
```
