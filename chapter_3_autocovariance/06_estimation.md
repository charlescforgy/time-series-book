# Estimation of Autocovariance, Autocorrelation, and Cross-Correlation

Thus far, we have dealt with the theoretical values for various forms of variance and correlation based on the assumption that we know the underlying process from which our observations have been drawn. In real life, we will usually be given the time series and then attempt to infer aspects such as the underlying process from statistical estimators. In this section we will develop techniques analogous to how sample covariance and sample correlation are used to estimate the true covariance and correlation in data science and statistics.

## Estimate Definitions

### Sample Autocovariance

The standard formula used in sources such as [](https://doi.org/10.1007/978-3-031-70584-7) and [](https://doi.org/10.1007/978-1-4419-0320-4) for sample autocovariance is

$$
\begin{equation}
\hat{\gamma}(h) \stackrel{\triangle}{=} \frac{1}{n}\sum_{t=0}^{n-h-1} (x_{t+h}-\bar{x})(x_t-\bar{x})
\end{equation}
$$ (sample-acovf-def)

where $\bar{x}$ is the sample mean and the "hat" notation indicates that $\hat{\gamma}$ is an estimate of the true population value. Note that we assume the same mean for $x_t$ and $x_{t+h}$.

**Problem:** Prove that the sample mean $\bar{x}$ is an unbiased estimator of true population mean $\mu_x$.


```{dropdown} Click to reveal solution
**Solution:** By the definition of bias, in order to prove that $\bar{x}$ is unbiased we must demonstrate that

$$
\mathbb{E}[\bar{x}] = \mu_x.
$$

We can demonstrate this as follows:

$$
\begin{equation}
\begin{split}
\mathbb{E}[\bar{x}] &= \mathbb{E}\Big[\frac{1}{n}\sum_{i=0}^{n-1}x_i\Big]\\
&= \frac{1}{n} \sum_{i=0}^{n-1}\mathbb{E}[x_i]\\
&= \frac{1}{n} \sum_{i=0}^{n-1}\mu_x\\
&= \frac{1}{n} n\mu_x\\
&= \mu_x
\end{split}
\end{equation}
$$

where we have exploited the linearity of expectation to go between the first and second lines.
```

### Sample Autocorrelation

Autocorrelation is estimated by defining the sample autocorrelation

$$
\begin{equation}
\hat{\rho}(h) \stackrel{\triangle}{=} \frac{\hat{\gamma}(h)}{\hat{\gamma}(0)}.
\end{equation}
$$ (sample-acf-def)

### Sample Cross-Covariance

The sample cross-covariance is defined as

$$
\begin{equation}
\hat{\gamma}_{x,y}(h) \stackrel{\triangle}{=} \frac{1}{n}\sum_{t=0}^{n-h-1} (x_{t+h}-\bar{x})(y_t-\bar{y})
\end{equation}
$$ (sample-ccovf-def)

and the sample cross-correlation as

$$
\begin{equation}
\hat{\rho}_{x,y}(h) \stackrel{\triangle}{=} \frac{\hat{\gamma}_{x,y}(h)}{\sqrt{\hat{\gamma}_x(0)\hat{\gamma}_y(0)}}.
\end{equation}
$$ (sample-ccf-def)

```{note} Use of Hat Notation
Going forward, we will generally drop the "hat" notation from $\gamma$ and $\rho$ when it is clear from context whether we are referring to the sample or population values.
```
## Properties of Estimators

### Positive Semidefiniteness

You may have noticed something strange about Eq. {eq}`sample-acovf-def`. For $n$ observations, we only have $n-h$ pairs to sum over, for example when $n=100$ and $h=2$ there are $98$ pairs consisting of $(x_0,x_2), (x_1,x_3),\ldots,(x_{97}, x_{99})$. Why then, do we divide Eq. {eq}`sample-acovf-def` by $n$ rather than $n-h$?[^1]

[^1]: While Eq. {eq}`sample-acovf-def` is the default method for calculating sample autocovariance in `statsmodels`, the autocovariance and autocorrelation (found in `statsmodels.tsa.stattools.acovf` and `statsmodels.tsa.stattools.acf`, respectively) have an argument `adjusted` that can be set to `True` in order to divide by $n-h$ instead. By default, both functions set `adjusted=False` (i.e. dividing by $n$ by default). The cross-covariance and cross-correlation functions in `statsmodels` default to `adjusted=True` as they do not share the same requirement to be positive semidefinite. Our definition in Eq. {eq}`sample-ccf-def` follows sources such as [](https://doi.org/10.1002/9781118619193) and [](https://doi.org/10.1007/978-3-031-70584-7).

The answer relates to the necessity of that the autocovariance matrix (and by extension autocorrelation matrix) [be positive semidefinite](03_stationarity.md#autocovariance-is-positive-semidefinite) in order to avoid the possibility of generating negative variances. To see that the form used in Eq. {eq}`sample-acovf-def` guarantees this property, let us sketch the proof from [](https://doi.org/10.1007/978-3-319-29854-2)[^2]. Let us define the matrix $\mathbf{X}3$ using the demeaned time series such that

[^2]: The full proof is unnecessary for our purposes, but can be found in Sec. 2.4.2 of the referenced text for those who are interested.

$$
\Big(\frac{1}{\sqrt{n}}\mathbf{X}\Big)^2 = \boldsymbol{\Gamma},
$$

where the elements of $X$ are the vectors $[\ldots, x_0-\bar{x}, x_1-\bar{x}, x_2-\bar{x}, \ldots]$ appropriately padded with zeros. Put slightly differently, we have now factored $\boldsymbol{\Gamma}$ such that

$$
\Gamma = \frac{1}{n}\mathbf{X}^T\mathbf{X}.
$$

Since we have expressed $\boldsymbol{\Gamma}$ as the square of $\mathbf{X}$, for any vector $\mathbf{v}$ we have

$$
\begin{equation}
\begin{split}
\mathbf{v}^T\boldsymbol{\Gamma}\mathbf{v} &= \frac{1}{n} \mathbf{v}^T \mathbf{X}^T \mathbf{X}\mathbf{v}\\
&= \frac{1}{n}\big(\mathbf{X}\mathbf{v}\big)^T \mathbf{X}\mathbf{v}\\
&= \frac{1}{n} ||\mathbf{X}\mathbf{v}||^2\\
&\geq0.
\end{split} 
\end{equation}
$$

The central point to understand is that **we could only factor $\boldsymbol{\Gamma}$ because we could pull out $\frac{1}{n}$ from every entry**. If we separately weighted each $\gamma(h)$ in $\boldsymbol{\Gamma}$ by $n-h$, we would no longer be able to express the autocovariance matrix $\boldsymbol{\Gamma}$ as the product of two other matrices. Consequently, we would have no guarantee that $\boldsymbol{\Gamma}$ was positive semidefinite, and could end up with negative variances.

### Bias and Consistency

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