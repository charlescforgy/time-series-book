# Estimation of Autocovariance, Autocorrelation, and Cross-Correlation

Thus far, we have dealt with the theoretical values for various forms of variance and correlation based on the assumption that we know the underlying process from which our observations have been drawn. In real life, we will usually be given the time series and then attempt to infer aspects such as the underlying process from statistical estimators. In this section we will develop techniques analogous to how sample covariance and sample correlation are used to estimate the true covariance and correlation in data science and statistics.

## Estimate of Autocovariance and Autocorrelation

The standard formula used in sources such as [](https://doi.org/10.1007/978-3-031-70584-7) and [](https://doi.org/10.1007/978-1-4419-0320-4) for sample autocovariance is

$$
\begin{equation}
\hat{\gamma}(h) \stackrel{\triangle}{=} \frac{1}{n}\sum_{t=0}^{n-h} (x_{t+j}-\bar{x})(x_t-\bar{x})
\end{equation}
$$ (sample-acovf-def)

where $\bar{x}$ is the sample mean and the "hat" notation indicates that $\hat{\gamma}$ is an estimate of the true population value. Note that we assume the same mean for $x_t$ and $x_{t+h}$.

Similarly, autocorrelation is estimated as

$$
\begin{equation}
\hat{\rho}(h) \stackrel{\triangle}{=} \frac{\hat{\gamma}(h)}{\hat{\gamma}(0)}.
\end{equation}
$$ (sample-acf-def)

```{note} Use of hat notation
Going forward, we will generally drop the "hat" notation from $\gamma$ and $\rho$ when it is clear from context whether we are referring to the sample or population values.
```

## Positive Semidefinite Matrices

You may have noticed something strange about Eq. {eq}`sample-acovf-def`. For $n$ observations, we only have $n-h$ pairs to sum over, for example when $n=100$ and $h=2$ there are $98$ pairs consisting of $(x_0,x_2), (x_1,x_3),\ldots,(x_{97}, x_{99})$. Why then, do we divide Eq. {eq}`sample-acovf-def` by $n$ rather than $n-h$?[^1]

[^1]: While Eq. {eq}`sample-acovf-def` is the default method for calculating sample autocovariance, in `statsmodels`, the autocovariance and autocorrelation (found in `statsmodels.tsa.stattools.acovf` and `statsmodels.tsa.stattools.acf`, respectively) have an argument `adjusted` that can be set to `True` in order to divide by $n-h$ instead. By default, both functions set `adjusted=False` (i.e. dividing by $n$ by default).

The answer relates to the necessity of that the autocovariance matrix (and by extension autocorrelation matrix) [be positive semidefinite](03_stationarity.md#autocovariance-is-positive-semidefinite) in order to avoid the possibility of generating negative variances. To see that the form used in Eq. {eq}`sample-acovf-def` guarantees this property, let us sketch the proof from [](https://doi.org/10.1007/978-3-319-29854-2) Sec. 2.4.2. Let us define the matrix $X$ using the demeaned time series as

$$
X \stackrel{\triangle}{=} \begin{bmatrix}
x_1 - \bar{x} & x_2 - \bar{x} & x_3 - \bar{x} & \cdots & x_{K+1} - \bar{x} \\
x_2 - \bar{x} & x_3 - \bar{x} & x_4 - \bar{x} & \cdots & x_{K+2} - \bar{x} \\
x_3 - \bar{x} & x_4 - \bar{x} & x_5 - \bar{x} & \cdots & x_{K+3} - \bar{x} \\
\vdots & \vdots & \vdots & \ddots & \vdots \\
x_{n-K} - \bar{x} & x_{n-K+1} - \bar{x} & x_{n-K+2} - \bar{x} & \cdots & x_n - \bar{x}
\end{bmatrix}
$$

where $K$ is the largest lag value we wish to use for $h$. The $(K+1)\times(K+1)$ autocovariance matrix $\Gamma$ can now be calculated as

$$
\Gamma = \frac{1}{n}X^TX.
$$

Since we have expressed $\Gamma$ as the square of $X$, for any vector $\mathbf{v}$ we have

$$
\begin{equation}
\begin{split}
\mathbf{v}^T\Gamma\mathbf{v} &= \frac{1}{n} \mathbf{v}^T X^T X\mathbf{v}\\
&= \frac{1}{n}\big(X\mathbf{v}\big)^T X\mathbf{v}\\
&= \frac{1}{n} ||X\mathbf{v}||^2\\
&\geq0.
\end{split} 
\end{equation}
$$

In constrast, if we weighted each $\gamma(h)$ value by $n-h$, we would no longer be able to express the autocovariance matrix $\Gamma$ as the product of two other matrices. Consequently, we would have no guarantee that $\Gamma$ was positive semidefinite, and could end up with negative variances.

Note that neither dividing by $n$ nor $n-h$ creates an unbiased estimator, i.e. in neither case is it true that

$$
\mathbb{E}[\hat{\gamma}(h)] = \gamma(h)
$$

though when dividing by $n-h$ the bias will be smaller.

The estimates are both, however, *consistent* in that in as $n\rightarrow\infty$

$$
\hat{\gamma}(h) \stackrel{P}{\rightarrow} \gamma(h).
$$

As observed in [](https://doi.org/10.1007/978-0-387-21736-9), most modern statistics texts consider consistency to be sufficient and are less concerned with estimators being unbiased.