# 6.2 Autoregressive Models (Under Construction)

We begin by discussing *autoregressive*, or *AR* models. As the name implies, AR models can be thought of as the application of linear regression to time series by regressing a time series onto lagged versions of itself[^1].

[^1]: Yule's original 1927 paper (@Yule_1927) introducing autoregressive models motivated the concept by drawing an analogy to a randomly perturbed oscillatory system—in effect discretizing the differential equations governing damped harmonic oscillators (though Yule himself did not directly use differential equation terminology). Modern students coming from a data science or statistics background are generally more comfortable interpreting AR models as form of linear regression in which the features are lagged versions of the time series itself.

## Random Walk to AR(1)

Recall that a [random walk](../chapter_1_introduction/04_basic_models.md#random-walk) is a model in which each time step's value is determined by the previous time step's value plus random noise, i.e.

$$
\begin{equation}
x_t = x_{t-1} + w_t.
\end{equation}
$$

A random walk [is not stationary](../chapter_3_autocovariance/02_autocovariance.md#random-walk-variance) due to its non-constant variance, making AR models not applicable. However, we could imagine a slightly different time series defined as

$$
\begin{equation}
x_t = \phi x_{t-1} + w_t, \qquad |\phi|<1.
\end{equation}
$$ (ar1-def)

Eq. {eq}`ar1-def` is a *first order autogressive process*, denoted as AR(1). Let us demonstrate that Eq. {eq}`ar1-def` describes a stationary process by iterating backwards:

$$
\begin{equation}
	\begin{split}
		x_t &= \phi x_{t-1} + w_t\\
		&=\phi (\phi x_{t-2}+ w_{t-1}) + w_t\\
		&=\phi^2 x_{t-2} + \phi w_{t-1} + w_t\\
		&=\phi^2(\phi x_{t-3} + w_{t-2}) + \phi w_{t-1} + w_t\\
		&\ldots\\
		&=\sum_{j=0}^{\infty} \phi^j w_{t-j}
	\end{split}
\end{equation}
$$ (ar1-iteration)

::::{tip} Problem
Why will Eq. {eq}`ar1-iteration` fail for $|\phi|\geq1$?

:::{dropdown} Click to reveal solution
**Solution:** Eq. {eq}`ar1-iteration` assumes that increasing powers of $\phi$ have diminishing magnitudes so that $\sum_{j=0}^{\infty} \phi^j w_{t-j}$ converges to a finite sum. For $|\phi|\geq1$, the sum will instead diverge to infinity. This is exactly what we observed plotting the variance of a random walk in {ref}`random-walk-1000-fig` of [section 3.2](../chapter_3_autocovariance/02_autocovariance.md).
:::
::::

### AR(1) Mean
From the last line of Eq. {eq}`ar1-iteration` , we conclude that the mean of an AR(1) process is

$$
\begin{equation}
\begin{split}
	\mathbb{E}[x_t] &= \mathbb{E}\Big[\sum_{j=0}^{\infty} \phi^j w_{t-j}\Big]\\
    &=\sum_{j=0}^{\infty} \phi^j \mathbb{E}[w_{t-j}]\\
    &=0.
\end{split}
\end{equation}
$$

### AR(1) Autocovariance
The autocovariance can be derived analogously

$$
\begin{equation}
	\begin{split}
		\gamma(h) &=\text{Cov}(x_{t+h}, x_t)\\
		&=\mathbb{E}\Big[\Big(\sum_{j=0}^{\infty}\phi^j w_{t+h-j}\Big)\Big(\sum_{k=0}^{\infty} \phi^k w_{t-k}\Big)\Big]\\
		&=\mathbb{E} [(w_{t+h} + \ldots + \phi^h w_t + \phi^{h+1}
		 		w_{t-1} + \ldots)(w_t + \phi w_{t-1} + \ldots)].
	\end{split}
\end{equation}
$$ (ar1-acf-derivation)

:::{note} Notation
In the first line of Eq. {eq}`ar1-acf-derivation` we have used $x_t$ and $x_{t+h}$ to refer to the *series* $x$ indexed by $t$ or $t+h$. Going forward, we will use $x_t$ to refer either to the series $x$ indexed by $t$ or the value of $x$ at time $t$ with the meaning determined by context.
:::

Noting that the covariance for the $w_t$'s is $\text{Cov}(w_i, w_j) = \delta_{ij}\sigma_w^2$, we can line up all non-zero terms as

$$
\begin{equation}
	\begin{split}
		\gamma(h) &= \sigma_w^2 \sum_{j=0}^{\infty} \phi^{h+j} \phi^j\\
		&=\sigma_w^2 \phi^h \sum_{j=0}^{\infty} \phi^{2j}\\
		&=\sigma_w^2 \frac{\phi^h}{1-\phi^2},
	\end{split}
\end{equation}
$$ (ar1-acf)

where we have cast the autocovariance as a [infinite geometric series](../chapter_2_background_math/02_geometric_series.md#related-infinite-sums). Provided that $\sigma_w^2$ is finite, Eq. {eq}`ar1-acf` will be finite for all values of $h$. Finally, the last line of Eq. {eq}`ar1-acf` depends solely on the separation $h$, completing the proof that Eq. {eq}`ar1-def` represents a stationary process.

### AR(1) Autocorrelation

Recall that the [autocorrelation $\rho(h)$](../chapter_3_autocovariance/04_autocorrelation.md) for a stationary process is given by

$$
\rho(h)\stackrel{\triangle}{=}\frac{\gamma(h)}{\gamma(0)}.
$$

Plugging in the result from Eq. {eq}`ar1-acf`, we obtain:

$$
\begin{equation}
	\begin{split}
        \rho(h) &= \frac{\sigma_w^2 \frac{\phi^h}{1-\phi^2}}{\sigma_w^2 \frac{1}{1-\phi^2}}\\
		&= \phi^h
	\end{split}
\end{equation}
$$ (ar1-acorf)

Eq. {eq}`ar1-acorf` highlights the signal characteristic of AR processes, namely that the autocorrelation $\rho(h)$ dies off exponentially with the number of lags. In the event that $\phi<0$, $\rho(h)$ will also die off in a sinusoidal fashion, alternating between positive and negative values.

::::{tip} Problem
We stated above that we cannot fit a non-stationary AR model, but we haven't explained what the problem is. A full exploration of the problem with non-stationary models requires a deeper math dive into ARMA models; however, we can begin to see some of the problems by creating a few simulated time series in this exercise inspired by [](10.1016/0304-4076(74)90034-7). Run the following code to explore spurious correlations in random walks. Note that `#%%` denotes a new code cell.

:::{code-cell} ipython3
import numpy as np
import plotly.express as px
#%%
np.random.seed(4400) # set seed for reproducibility
SIZE = 1000
#%%
# Create random walks by summing random noise.
rw_1 = np.cumsum(a=np.random.normal(loc=0, scale=1, size=SIZE))
rw_2 = np.cumsum(a=np.random.normal(loc=0, scale=1, size=SIZE))
print("Correlation between random walks: ", np.corrcoef(rw_1, rw_2)[1,0])
#%%
# examine quick plot
px.line(x=np.arange(SIZE), y=[rw_1, rw_2])
#%%
# In this cell we will create two AR(1) processes that are close to random walks in terms of phi values.
PHI = 0.95
ar1_1 = np.empty(SIZE)
ar1_1[0] = 0
for idx in range(1, SIZE):
    ar1_1[idx] = PHI*ar1_1[idx-1] + np.random.normal(loc=0, scale=1)
ar1_2 = np.empty(SIZE)
ar1_2[0] = 0
for idx in range(1, SIZE):
    ar1_2[idx] = PHI*ar1_2[idx-1] + np.random.normal(loc=0, scale=1)
print("Correlation between AR(1) processes: ", np.corrcoef(ar1_1, ar1_2)[1,0])
#%%
px.line(x=np.arange(SIZE), y=[ar1_1, ar1_2])
:::
What happens if you run the code multiple times with different seeds? How frequently do you observe a spurious high correlation between the random walks? What about between the AR(1) processes?
::::

### AR(1) with Nonzero Mean

Up to this point, we've assumed that our AR(1) model has a mean of $0$, in which case $\phi$ exerts a sort of gravitational pull to bring values back to $0$ by damping out previous noise. We can easily extend this to an AR(1) process with a nonzero mean $\mu$ by subtracting the mean from each observation

$$
\begin{equation}
    \begin{split}
        x_t-\mu &= \phi(x_{t-1}-\mu) + w_t\\
        x_t &= \phi x_{t-1} -\phi \mu + \mu + w_t\\
        &= \phi x_{t-1} + (1-\phi)\mu + w_t\\
        &= \alpha + \phi x_{t-1} + w_t,
    \end{split}
\end{equation}
$$

where $\alpha \stackrel{\triangle}=1-\phi$ functions the same way that an intercept term would in standard linear regression.

::: {important}
The intercept term in an AR model is *not* equal to the expectation value of the series, but the two terms are related.
:::

## AR(p) Models

It's straightforward to generalize AR models to higher order *AR(p)* models with $p\geq1$ by regressing the time series onto versions of itself of increasing lag. A general AR(p) model is given as

$$
x_t = \phi_1 x_{t-1} + \phi_2 x_{t-2} + \ldots + \phi_p x_{t-p} + w_t, \qquad p\geq1.
$$ (ar-p-def)

As before, the intercept term for a series with a nonzero mean is given as

$$
\begin{equation}
    \begin{split}
		x_t-\mu &= \phi_1 (x_{t-1}-\mu) + \phi_2 (x_{t-2}-\mu) + \ldots + \phi_p (x_{t-p}-\mu) + w_t\\
		x_t &= \mu(1-\phi_1-\phi_2-\ldots-\phi_p)  + \phi_1 x_{t-1} + \phi_2 x_{t-2} + \ddots + \phi_p x_{t-p} + w_t\\
		&= \alpha + \phi_1 x_{t-1} + \phi_2 x_{t-2} + \ldots + \phi_p x_{t-p} + w_t,     
    \end{split}
\end{equation}
$$

where as before the intercept is given as $\alpha \stackrel{\triangle}=\mu(1-\phi_1-\phi_2-\ldots-\phi_p)$.

Deriving the theoretical autocovariance and autocorrelation functions of an AR(p) process is somewhat more involved than [for an AR(1) model](#ar1-autocovariance). We will defer the derivation until after we have covered moving average models and methods for converting between them and autoregressive models.

## AR Models in Backshift Notation