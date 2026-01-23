# 3.3 Stationarity

## Strict vs. Weak Stationarity

Now that we are familiar with the autocovariance, we can address *stationarity*, one of the most central concepts in time series analysis. Loosely, a stationary time series is one that does not demonstrate any trend or seasonal variation. More concretely, there are two distinct definitions of stationarity:

1. *Strict stationarity* is defined as a time series for which the probabilistic behavior of any subset of observations is identical, i.e.

$$
\mathbb{P}_{x}(x_{t_1}<c_1, x_{t_2}<c_2,...,x_{t_k}<c_k) = \mathbb{P}_{x}(x_{t_{1+h}}<c_1, x_{t_{2+h}}<c_2,...,x_{t_{k+h}}<c_k).
$$

2. *Weak stationarity* is defined as a time series for which the following hold:

	i. The mean $E[x_t]$ is finite and constant across all times $t$.
    
    ii. The variance $\mathbb{V}(x_t)=\gamma(t, t)$ is finite and constant across all times $t$.
    
    iii. The autocovariance is a function of lag alone and not of absolute time, $\gamma(s, t)= \gamma(s+v, t+v)=\gamma(|s-t|)$.

An example of a strictly stationary series would be pure white noise. Stock returns (*not* values) are also reasonably approximated as being strictly stationary. Weakly stationary processes are far more prevalent; in coming chapters we will encounter numerous examples from fields such as medicine and econometrics.

### Strict to Weak Stationarity

It should be obvious that demonstrating weak stationarity does not demonstrate strict stationarity[^1]. Interesting, the converse also does not necessarily hold. Consider the white noise process defined by the standard Cauchy distribution:

[^1]: It can be proved that if the process that generates $x_t$ is a Gaussian process, then weak stationarity does demonstrate strict stationarity. This is not overly useful in practice as it requires us to know $x_t$'s hidden generating process.

$$
x_t \sim \text{Cauchy}(0,1)
$$

$x_t$ is strictly stationary as every point is drawn from the same probability distribution as every other point. However, $x_t$ is *not* weakly stationary as the Cauchy distribution lacks a finite variance. That said, any strongly stationary finite variance process is also weakly stationary.

## Advantages of Weak Stationarity

In practice, we generally do not use strict stationarity for two major reasons:

1. Establishing strict stationarity requires knowing the underlying probability distribution (or at least having a confident guess to it). In contrast, weak stationarity is established solely via observables in the form of the mean, variance, and autocovariance.
2. Strict stationarity is generally too strict for most purposes. We will see in coming chapters that methods requiring stationarity can be used with weakly stationary time series.

:::{note} Stationarity
Following sources such as [](https://doi.org/10.1007/978-3-031-70584-7) and [](https://doi.org/10.1007/978-1-4419-0320-4), we will use the term "stationary" to refer to weak stationarity and specify "strict stationarity" when necessary.
:::

::::{tip} Problem
Consider the process $v_t$ from [Chapter 1](../chapter_1_introduction/04_basic_models.md#moving-average) defined as
$$v_t\stackrel{\triangle}{=}\frac{1}{3}(w_{t-1} + w_t + w_{t+1})$$
where $w_t\sim \text{iid}(0, \sigma_w^2)$. Is this process weakly stationary? Is it strictly stationary?

:::{dropdown} Click to reveal solution
**Solution:** The expectation value (mean) of $v_t$ is always $0$. As demonstrated in [the previous section](02_autocovariance.md#mean-and-autocovariance-of-moving-average), the autocovariance is exclusively a function of $|s-t|$. Thus the process is weakly stationary.

$v_t$ is also strictly stationary as every instance of $v_t$ has is drawn from same distribution as every other instance. Note that demonstrating strict stationarity for a finite variance process also proves weak stationarity.
:::
::::

## Autocovariance of a Stationary Process

As discussed [above](03_stationarity.md#strict-vs-weak-stationarity), the autocovariance $\gamma(s,t)$ of a stationary process depends exclusively on the lag $h=|s-t|$. Consequently, when dealing with stationary processes we will simply use the notation

$$
\gamma(h), \qquad h=|s-t|
$$

where $\gamma(0)$ is the variance, $\gamma(1)$ is the autocovariance at lag $1$, and so on. Note that 

$$
\gamma(-h) = \gamma(|t-s|) =  \gamma(|s-t|) = \gamma(h),
$$

consequently, we will only calculate $\gamma(h)$ for $h\geq0$.

::::{tip} Problem
What can we say about the relationship between $\gamma(h)$ and $\gamma(0)$ given that the relevant time series is stationary?

:::{dropdown} Click to reveal solution
**Solution:** As demonstrated in [Chapter 2](../chapter_2_background_math/04_variance_covariance.md#geometric-inequality), for any finite variance random variables $X$ and $Y$

$$
|\text{Cov}(X, Y)| \leq \sqrt{\mathbb{V}(X)\mathbb{V}(Y)}.
$$

As $\gamma(s,s) = \gamma(t,t)=\gamma(0)$ and $\gamma(s,t)=\gamma(|s-t|)=\gamma(h)$, we conclude

$$
|\gamma(h)| \leq \gamma(0).
$$

This is turn guarantees that the value $\frac{\gamma(h)}{\gamma(0)}$ lies in the interval $[-1,1]$.
:::
::::

## Autocovariance of a Stationary Process is Positive Semidefinite

We are now in a position to prove a property of autocovariance that will be useful in future chapters. The autocovariance matrix $\boldsymbol{\Gamma}$ is defined as

$$
\begin{equation}
\boldsymbol{\Gamma}=\begin{bmatrix}
\gamma(0) && \gamma(1) && \ldots && \gamma(h)\\
\gamma(1) && \gamma(0) && \ldots && \gamma(h-1)\\
\vdots    && \vdots    && \ddots && \vdots\\
\gamma(h) && \gamma(h-1) && \ldots && \gamma(0)\\ 
\end{bmatrix}.
\end{equation}
$$

$\boldsymbol{\Gamma}$ for a stationary process, like standard covariance matrices, is *positive semidefinite.* A positive semidefinite matrix is defined as a symmetric matrix[^2] $\mathbf{X} \in \mathbb{R}^{n\times n}$ with the property that for any vector $\mathbf{v}\in\mathbb{R}^{n}$

[^2]: The definition of positive semidefiniteness can be expanded to include complex vectors and matrices, but that will not be necessary for this book.

$$
\begin{equation}
\mathbf{v}^T\mathbf{X}\mathbf{v} \geq 0.
\end{equation}
$$

To demonstrate that $\boldsymbol{\Gamma}$ is positive semidefinite, let

$$
\mathbf{\ell}\stackrel{\triangle}{=} \ell_0 x_t+\ell_1 x_{t-1}+\ldots+\ell_h x_{t-h}.
$$

As variance cannot be negative, we have

$$
\mathbb{V}(\mathbf{\ell}) = \mathbb{V}(\ell_0 x_t+\ell_1 x_{t-1}+\ldots+\ell_h x_{t-h}) \geq 0,
$$
or using the [variance of a sum](../chapter_2_background_math/04_variance_covariance.md#variance-of-sums-of-random-variables)

$$
\begin{equation*}
\begin{split}
\mathbb{V}(\mathbf{\ell}) &= \sum_{i=0}^h\sum_{j=0}^h \ell_i \ell_j \text{Cov}(x_{t-i}, x_{t-j})\\
&= \sum_{i=0}^h\sum_{j=0}^h \ell_i \ell_j \gamma(|i-j|)\\
&= \begin{bmatrix}
\ell_0, && \ell_1, && \ldots && \ell_{h} 
\end{bmatrix}
\begin{bmatrix}
\gamma(0) && \gamma(1) && \ldots && \gamma(h)\\
\gamma(1) && \gamma(0) && \ldots && \gamma(h-1)\\
\vdots    && \vdots    && \ddots && \vdots\\
\gamma(h) && \gamma(h-1) && \ldots && \gamma(0)\\ 
\end{bmatrix}
\begin{bmatrix}
\ell_0 \\ \ell_1 \\ \vdots \\ \ell_{h} 
\end{bmatrix}\\
&=\mathbf{\ell}^T\boldsymbol{\Gamma}\mathbf{\ell}\\
&\geq 0.
\end{split}
\end{equation*} 
$$

As the above holds for any possible vector $\mathbf{\ell} \in \mathbb{R}^{h+1}$ we conclude that $\boldsymbol{\Gamma}$ must be positive semidefinite.

:::{note} Eigenvalues
It can be demonstrated that an equivalent definition for "positive semidefinite" is the requirement that all eigenvalues be non-negative.
:::