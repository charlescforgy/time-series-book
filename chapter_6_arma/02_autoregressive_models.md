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

In the next section, we will see that Eq. {eq}`ar1-iteration` is the infinite *moving average*, or MA($\infty$) representation of an AR process. For our purposes, we can think of it as a way to shed light on the behavior of an AR process.

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
The autocovariance can be derived analogously. For [any stationary AR(1) process with mean zero](../chapter_3_autocovariance/02_autocovariance.md#notation), $\gamma(h) =\text{Cov}(x_{t+h}, x_t)$. Should the mean not be zero, replace $x_t$ with $x_t-\mu_x$. We can then derive $\gamma(h)$ as

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

:::{important} Exponential Decay of Autocorrelation
Eq. {eq}`ar1-acorf` highlights the signal characteristic of stationary AR processes, namely that the autocorrelation $\rho(h)$ dies off exponentially with the number of lags. 
:::

In the event that $\phi<0$, $\rho(h)$ will also die off in a sinusoidal fashion, alternating between positive and negative values. These two possibilities are demonstrated in {ref}`ar1-acf-pos-neg`.

:::{figure} images/ar1_acf_pos_neg.png
---
width: 80%
name: ar1-acf-pos-neg
---
Theoretical autocorrelation for stationary AR($1$) processes with $\phi>0$ and $\phi<0$.
:::

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

where $\alpha \stackrel{\triangle}=\mu(1-\phi)$ functions the same way that an intercept term would in standard linear regression.

::: {important}
The intercept term in an AR model is *not* equal to the expectation value of the series, but the two terms are related.
:::

## AR($p$) Models

It's straightforward to generalize AR models to higher order *AR($p$)* models with $p\geq1$ by regressing the time series onto versions of itself of increasing lag. A general AR($p$) model is given as

$$
x_t = \phi_1 x_{t-1} + \phi_2 x_{t-2} + \ldots + \phi_p x_{t-p} + w_t, \qquad p\geq1.
$$ (ar-p-def)

A series with a nonzero mean is handled as

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

Deriving the theoretical autocovariance and autocorrelation functions of an AR($p$) process is somewhat more involved than [for an AR(1) model](#ar1-autocovariance). We will defer the derivation until after we have covered representing AR models as [infinite series of noise terms in section 3](04_arma.md).

## AR Models in Backshift Notation

### Autoregressive Operator

Recall from Eq. {eq}`backshift-def` in [section 4.3](../chapter_4_enforcing_stationarity/03_difference.md#backshift-operator) that the backshift operator $\mathbb{B}$ increments a time series back by one time step. Using this definition, we may express Eq. {eq}`ar1-def` as

$$
\begin{equation}
    \begin{split}
        &x_t = \phi\,\mathbb{B}\,x_{t} + w_t\\
        &x_t - \phi\,\mathbb{B}\,x_{t} = w_t\\
        &(1 - \phi\,\mathbb{B})\,x_{t} = w_t.\\
    \end{split}
\end{equation}
$$

We can expand this definition to represent AR($p$) models using backshift notation as

$$
\begin{equation}
	(1-\phi_1 \mathbb{B} - \phi_2 \mathbb{B}^2 -\ldots-\phi_p \mathbb{B}^p)x_t = w_t,
\end{equation}
$$ (ar-p-backshift)

or in more compact form

$$
\begin{equation}
	\phi(\mathbb{B})x_t = w_t,
\end{equation}
$$ (ar-operator)

where $\phi(\mathbb{B})$ is the *autoregressive operator* defined as:

$$
\begin{equation}
	\phi(\mathbb{B}) \stackrel{\triangle}{=} 1-\phi_1 \mathbb{B} -\phi_2 \mathbb{B}^2 - \ldots - \phi_p \mathbb{B}^p.
\end{equation}
$$

Finally, for $|\phi|<1$, we can define an inverse autoregressive operator $\phi^{-1}(\mathbb{B})$

$$
\begin{equation}
	\phi^{-1}(\mathbb{B})\phi(\mathbb{B})x_t = \phi^{-1}(\mathbb{B})w_t
\end{equation}
$$

or

$$
\begin{equation}
	x_t = \phi^{-1}(\mathbb{B})w_t.
\end{equation}
$$

Given that $\phi^{-1}(\mathbb{B}) = \frac{1}{1-\phi \mathbb{B}}$ and $|\phi|<1$, we can represent $\phi^{-1}(\mathbb{B})$ as a [infinite geometric series](../chapter_2_background_math/02_geometric_series.md#infinite-geometric-series)

$$
\begin{equation}
    \phi^{-1}(\mathbb{B}) = 1 + \phi \mathbb{B} + \phi^2 \mathbb{B}^2 + \phi^3 \mathbb{B}^3 + \ldots
\end{equation}
$$ (inverse-def)

::::{tip} Problem
Why does Eq. {eq}`inverse-def` require $|\phi|<1$?

:::{dropdown} Click to reveal solution
**Solution:** A geometric series will only converge for [values with an absolute value less than $1$](../chapter_2_background_math/02_geometric_series.md#infinite-geometric-series), and will also have a division by zero error at $1$. Conceptually, for $|\phi|>1$, Eq. {eq}`inverse-def` will blow up to infinity as we raise $\phi$ to higher and higher powers. At $|\phi|=1$, even though $\phi$ itself will not go to infinity, the overall infinite summation of a non-decreasing series will still go to infinity.
:::
::::

### Causal AR Models

What is the purpose of representing AR processes in the form of Eq. {eq}`ar-p-backshift` or {eq}`ar-operator`? To answer this, begin by noting that Eq. {eq}`inverse-def` implies that $x_t = w_t + \phi w_{t-1} + \phi^2 w_{t-2}+\ldots$, as derived in Eq. {eq}`ar1-iteration`. This is an example of a *causal model*, in which a series can be treated as being generated by an infinite series of noise terms. Random walks, on the other hand, are non-causal as they will increase without bound as we include more and more time steps. Series with $|\phi|>1$ are referred to as *explosive* as past noise becomes arbitrarily magnified as $\phi$ is raised to increasing powers—exactly what happens in a physical explosion (at least until the fuel is exhausted).

Non-causal processes such as unit root and explosive processes are not stationary, making many of the tools used in time series analysis unsuitable. We can easily determine if an AR(1) process is causal by examining $\phi$, how might we do the same for higher order AR($p$) models? Imagine if we could factor an AR model such that

$$
\begin{equation}
	(1-\phi_1 \mathbb{B} - \phi_2 \mathbb{B}^2 -\ldots-\phi_p \mathbb{B}^p)x_t=(1-\varphi_1^{\prime} \mathbb{B})(1-\varphi_2^{\prime}  \mathbb{B})\ldots(1-\varphi_p^{\prime}  \mathbb{B})x_t.
\end{equation}
$$

Given such a factoring, we could quickly determine if our AR($p$) model was causal—and hence stationary—simply by confirming that all $\varphi^{\prime} $'s have an absolute value less than $1$. While there is no guarantee this will be possible for real $\varphi^{\prime}$'s, the fundamental theorem of algebra does ensure this is possible for complex values[^1].

[^1]: The fundamental theorem of algebra states that any polynomial of degree $p$ has *exactly* $p$ roots (provided we allow for complex roots). The theorem allows for a single root to appear multiple times such as in the case of $1+2x+x^2=(1+x)^2$, which has the root $x=-1$ with multiplicity $2$.

 Let's explore this with an concrete example. Consider the second order autoregressive, or AR(2) model defined as

$$
\begin{equation}
	x_t = 1.25x_{t-1} - 0.375 x_{t-2} + w_t.
\end{equation}
$$ (ar2-real-roots)

Looking at Eq. {eq}`ar2-real-roots`, it's not immediately obvious if its $\varphi^{\prime}$'s are greater than one. Let's recast it using the autoregressive operator $\phi(\mathbb{B})$

$$
\begin{equation}
	\begin{split}
		x_t = 1.25\,x_{t-1} - 0.375\, x_{t-2} + w_t\\
		x_t - 1.25\,x_{t-1} + 0.375\, x_{t-2} = w_t\\
		(1-1.25\,\mathbb{B} + 0.375\,\mathbb{B}^2)x_t = w_t.
	\end{split}
\end{equation}
$$ (ar2-operator)

Evidently, $\phi(\mathbb{B})=1-1.25\,\mathbb{B} + 0.375\,\mathbb{B}^2=1-\frac{5}{4}\,\mathbb{B} + \frac{3}{8}\,\mathbb{B}^2$. Now comes the crucial step, **we replace the backshift operator with a variable, call it $z$, and solve for the zeros of the polynomial**.

$$
\begin{equation}
	\begin{split}
		1-\frac{5}{4}\,z + \frac{3}{8}\,z^2 &= 0\\
		(1-\frac{1}{2}\,z)(1-\frac{3}{4}\,z) &= 0\\
		z = 2 \quad \text{or} \quad z=\frac{4}{3}.
	\end{split}
\end{equation}
$$

Eq. {eq}`ar2-real-roots` is stationary because the $\varphi^{\prime}$'s of $-\frac{1}{2}$ and $-\frac{3}{4}$ have absolute values less than one (lie inside the unit circle[^2]). Equivalently, the roots of $\phi(\mathbb{B})$ of $2$ and $\frac{4}{3}$ have absolute values *greater* than one (lie outside the unit circle).

[^2]: The unit circle is simply the set of all complex numbers such that $|a+bi|=1$, or [equivalently](../chapter_2_background_math/03_eulers_formula.md#eulers-formula-the-jewel-of-mathematics) all complex numbers of the form $e^{i\theta},\, \theta\in[0,2\pi)$.

::: {important} Unit Circle and Stationarity
It is an unfortunate fact of time series analysis that both the definition requiring all $\varphi^{\prime}$'s to lie inside the unit circle and the definition requiring all roots of $z$ to lie outside the unit circle are both in use[^3]. It is not always obvious which definition is being used. We will follow sources such as `statsmodels` and [](https://doi.org/10.1007/978-3-031-70584-7) and require all roots to lie *outside* the unit circle, but be aware that this convention is not universal.
:::

[^3]: The actual requirement is derived by using the reciprocal definition of the roots $z$ from what we used. Our definition using $\varphi^{\prime}$ expresses the same idea from a different angle.

### Sign of $\phi$ and Complex Roots

[As seen above](#ar1-autocorrelation), the hallmark characteristic of stationary AR models is the presence of exponentially decaying autocovariance (and consequently autocorrelation) functions. For an AR($p$) process with $p\geq2$, the decay may also exhibit **sinusoidal oscillations, potentially with a period greater than $p$. 

:::{figure} images/ar2_complex_roots.png
---
width: 80%
name: ar2-complex-roots
---
Theoretical autocorrelation for stationary AR($2$) processes with complex roots.
:::

<iframe src="/time-series-book/ar_process_demo.html" width="100%" height="520" frameborder="0" scrolling="no"></iframe>

If the above fails to render correctly in your browser you can also open the demo as a new browser window using the `Open Demo in a New Tab ↗` button at the top of the frame. Note that you may need to enable popups for this to work.

By the quadratic formula, an AR(2) process will be stationary only if

$$
\begin{equation}
\Bigg|\frac{\phi_1\pm\sqrt{\phi_1^2+4\phi_2}}{-2\phi_2}\Bigg| > 1
\end{equation}
$$ (ar2-quadratic-formula)

Eq. {eq}`ar2-quadratic-formula` can be broken into three distinct requirements:
1. $\phi_1 + \phi_2 < 1$
2. $\phi_2 - \phi_1 < 1$
3. $\phi_2 > -1$

Some sources state the third requirement as $|\phi_2|<1$ though this is not strictly necessary as combining the first two requirements above already enforces $\phi_2<1$.

From Eq. {eq}`ar2-quadratic-formula` we see that the roots of an AR(2) model will be complex if $\phi_1^2+4\phi_2<0$, i.e. if $\phi_2<-\frac{\phi_1^2}{4}$. In [Sec. 4 of this chapter](04_arma.md) we will see that AR processes with complex roots have a special property of exhibiting a "pseudo-seasonality." These conditions are depicted in the {ref}`ar2-stationarity-region`.

:::{figure} images/ar2_stationarity.pdf
---
width: 80%
name: ar2-stationarity-region
---
Values of $\phi_1$ and $\phi_2$ for AR(2) process demonstrating the boundary for stationarity and real/complex roots.
:::

### Higher Order Causal Models

We could expand the process above for finding unit roots to complex polynomials and higher order AR($p$) models, but in practice there's no need to do this by hand. `statsmodels.tsa.arima_process.ArmaProcess` provides theoretical properties of AR (and more broadly ARMA) models for us. The following code demonstates using this module both to determine stationarity writ large and to extract the roots of an AR model. Note that the sign convention follows $\phi(\mathbb{B})$ from Eq. {eq}`ar2-operator`, not Eq. {eq}`ar2-real-roots`.

::: {code-cell} ipython3
from statsmodels.tsa.arima_process import ArmaProcess
ar2 = ArmaProcess(ar=[1, -1.25, 0.375], # use phi values defined via phi(B)
                  ma = None, # pure AR process, no MA component
                 )
print(f"AR(2) model is stationary: {ar2.isstationary}")
print(f"Roots of AR(2) model: {ar2.arroots}")
:::

:::: {tip} Problem
What would the root(s) of a random walk be? Repeat the above to confirm your calculation. Find the roots of

$$
x_t = -x_{t-1} - 0.5\,x_{t-2} + w_t.
$$

Is this AR(2) process stationary?

:::{dropdown} Click to reveal solution
**Solution:** A random walk has $\phi(\mathbb{B})=1-\mathbb{B}$, resulting in it having a *unit root* of 1. The AR(2) process has roots of $-1\pm i$. The roots have an absolute value of $\sqrt{2}$, which is outside the unit circle, making the process stationary.
:::
::::

::: {note} Unit Root vs. Explosive Process
While the mathematical requirement is that all roots lie outside the unit circle (or all $\varphi^{\prime}$'s lie inside the unit circle), in practice we are usually only concerned with differentiating between processes with all roots outside the unit circle versus processes with at least one root *on* the unit circle, i.e. a *unit root*. This is because a root inside the unit circle corresponds to an explosive process, which are both uncommon in time series analysis and generally self-evident when they do occur. Consequently, we usually only worry about the existence of a unit root corresponding to random walk type behavior.
:::

## Where do AR Processes Arise?

::::{tip} Problem
In this problem we will explore fitting AR($p$) models to the sunspots dataset the comes with `statsmodels` and using both [AIC and BIC](../chapter_2_background_math/05_information_criteria.md) to rank solutions.

:::{code-cell} ipython3
import numpy as np
import pandas as pd
import plotly.express as px
from statsmodels.datasets import sunspots
from statsmodels.tsa.arima.model import ARIMA
from statsmodels.tsa.arima_process import ArmaProcess
#%%
print(sunspots.NOTE)
#%%
sunspots_df = sunspots.load_pandas().data
sunspots_df["YEAR"] = pd.to_datetime(sunspots_df["YEAR"], format='%Y', )
sunspots_df.set_index("YEAR", inplace=True, drop=True)
sunspots_df = sunspots_df.asfreq('YS') # Explicitly set the frequency to 'Year Start'
sunspots_df.head()
#%%
# examine quick plot
px.line(sunspots_df)
#%%
# create and save AR(p) models for p=1,2,3,4
ar_model_dict = {}
for p in range(1,5):
  print(f"AR Model for p={p}")
  ar_model_dict[p] = ARIMA(sunspots_df, order=(p,0,0)).fit()
  print("AIC: ", ar_model_dict[p].aic)
  print("BIC: ", ar_model_dict[p].bic)
  print("\n")
:::

You should see that the AR(1) performs notably worse, wheres the AR(2), AR(3), and AR(4) models are roughly tied. AIC slightly favors the AR(3), whereas (consistent with its tendency to choose smaller models) BIC very slightly favors the AR(2), though in both cases the difference between models for $p>1$ is [small enough that I wouldn't read too much into it](../chapter_2_background_math/05_information_criteria.md#significance-of-aic-or-bic-values). Let's look at a summary and the theoretical properties of the AR(2) model. Don't worry if you're not yet familiar with some of the terms in the summary, we'll get to them.

:::{code-cell} ipython3
# print model summary
ar_model_dict[2].summary()
#%%
# create a theoretical ARMA model to explore its properties
phi_1 = ar_model_dict[2].params['ar.L1']
phi_2 = ar_model_dict[2].params['ar.L2']
ar2 = ArmaProcess(ar=[1, -phi_1, -phi_2], # note change of sign of phi values
                  ma = None, # pure AR process, no MA component
                 )
print(f"AR(2) model is stationary: {ar2.isstationary}")
print(f"Roots of AR(2) model: {ar2.arroots}")
# get pseudo-periodicity
print(f"Pseudo-period: {round(2*np.pi/np.abs(np.angle(ar2.arroots[0])), 2)} years")
:::

Is the pseudo-period consistent with the plot from above? Finally, let's compare the sample autocovariance to the theoretical autocovariance of our proposed AR(2) model.

:::{code-cell} ipython3
sample_acf = plot_acf(sunspots_df, title="Sample ACF")
sample_acf.show()

theoretical_acf_plot = plot_acf(ar2.acf(), adjusted=False, title='Theoretical ACF')
theoretical_acf_plot.show()
:::
Do the two plots appear to agree?
::::