# 6.4 ARMA Models (Under Construction)

In this section, we will combine AR and MA models to create **autoregressive moving average** (ARMA) models. A model is defined as ARMA($p, q$) if it is stationary and can be expressed as

$$
\begin{equation}
	x_t = \phi_1 x_{t-1} + \ldots + \phi_p x_{t-p} + w_t + \theta_1 w_{t-1} + \ldots + \theta_q w_{t-q}.
\end{equation}
$$

If $\mathbb{E}[x_t]\neq 0$, we instead define the model as

$$
\begin{equation}
	x_t = \alpha +\phi_1 x_{t-1} + \ldots + \phi_p x_{t-p} + w_t + \theta_1 w_{t-1} + \ldots + \theta_q w_{t-q},
\end{equation}
$$

with $\alpha = \mu(1-\phi_1-\ldots-\phi_p)$. In general, we will assume that the mean has been subtracted from our time series so that $\mu=\alpha=0$.

In the process of developing ARMA models we will build tools that allow us to better understand the autocorrelation of various processes and how to apply this knowledge to practical data science applications.

## Operator Notation and Parameter Redundancy

In keeping with the operators derived in sections [6.2](../chapter_6_arma/02_autoregressive_models.md#autoregressive-operator) and [6.3](../chapter_6_arma/03_moving_average_models.md#moving-average-operator), an ARMA($p, q$) model may be expressed in operator form as

$$
\begin{equation}
	\phi(\mathbb{B})x_t = \theta(\mathbb{B})w_t
\end{equation}
$$ (arma-operator)

Eq. {eq}`arma-operator` presents a potential problem inasmuch as we can multiply both sides by the same term and still maintain a valid model, for example

$$
\begin{equation}
	\eta(\mathbb{B})\phi(\mathbb{B})x_t = \eta(\mathbb{B})\theta(\mathbb{B})w_t
\end{equation}
$$ (parameter-redundancy)
    
To give a concrete example, take the white noise process $x_t = w_t$, and multiply it by some $\eta(\mathbb{B})$, say $(1-0.5\mathbb{B})$:

$$
\begin{equation}
	(1-0.5B)x_t = (1-0.5B)w_t,
\end{equation}
$$

or

$$
\begin{equation}
	x_t = 0.5x_{t-1} - 0.5w_{t-1} + w_t,
\end{equation}
$$ (redundant-arma)

which is now an ARMA($1,1$) model, despite only describing white noise. You might be tempted to rely on the statistical significance of the fitted $\phi$ and $\theta$ values to identify parameter redundancy. The following code helps demonstrate why we cannot use this tactic. Note that it is particularly strongly recommended that you run this code yourself to appreciate parameter redundancy.

::: {code-cell} ipython3
import numpy as np
import matplotlib.pyplot as plt
from statsmodels.tsa.arima.model import ARIMA

#%%
# initialize empty lists
phi_estimates = []
theta_estimates = []
phi_significance = []
theta_significance = []
# create 100 white noise simulations and fit ARMA(1,1) model
for idx in range(100):
    white_noise = np.random.normal(size=1000)
    arma_1_1 = ARIMA(white_noise, order=(1,0,1)).fit()
    phi_estimates.append(arma_1_1.params[1]) # estimated phi
    theta_estimates.append(arma_1_1.params[2]) # estimated theta
    phi_significance.append(arma_1_1.pvalues[1]) # p-value of estimated phi
    theta_significance.append(arma_1_1.pvalues[2]) # p-value of estimated theta

#%%
# plot phi and theta values
fig, ax = plt.subplots()
ax.scatter(phi_estimates, theta_estimates, c=theta_significance, cmap='viridis')
ax.set_xlabel('ϕ Estimates', fontsize=18)
ax.set_ylabel('θ Estimates', fontsize=18)
ax.set_title('Scatter Plot of ϕ vs θ Estimates', fontsize=20)

#%%
# plot histogram of p-values
fig, ax = plt.subplots()
ax.hist(theta_significance, bins=50, alpha=0.7, label='θ Estimates')
ax.hist(phi_significance, bins=50, alpha=0.7, label='ϕ Estimates')
ax.set_xlabel('Parameter Significance', fontsize=18)
ax.set_ylabel('Frequency', fontsize=18)
ax.set_title('Histogram of ϕ and θ p-values', fontsize=20)
ax.legend()
:::

In the above code, you should see that in almost all cases $\theta\approx -\phi$, with values in $(-1,1)$ (by default, `statsmodels` will attempt to enforce both stationarity and invertibility). You should also see that the majority of all estimates were statistically significant.

## ARMA to AR($\infty$) and MA($\infty$)

### AR($\infty$) and MA($\infty$) Terminology

Before deriving them, we first define the terminology for AR($\infty$) and MA($\infty$) representations. An MA($\infty$) representation is traditionally denoted using the variable $\psi$ as

$$
\begin{equation}
\begin{split}
x_t &= \sum_{j=0}^{\infty}\psi_j\, w_{t-j}\\
&= \sum_{j=0}^{\infty}\psi_j\mathbb{B}^j\,w_t\\
&= \psi(\mathbb{B})\,w_t
\end{split}
\end{equation}
$$

where $\psi_0\stackrel{\triangle}{=}1$.

Following the same logic, the AR($\infty$) representation, often denoted by the variable $\pi$ (apologies for using $\pi$ as a variable instead of a constant), is given as

$$
\begin{equation}
\begin{split}
w_t &= \sum_{j=0}^{\infty}\pi_j\, x_{t-j}\\
&= \sum_{j=0}^{\infty}\pi_j\mathbb{B}^j\,x_t\\
&= \pi(\mathbb{B})\,x_t
\end{split}
\end{equation}
$$

where $\pi_0\stackrel{\triangle}{=}1$.

## Autocovariance and Autocorrelation of AR($p$) Models

### Deriving Weights for Causal Process

We've seen that the autoregressive operator $\phi(\mathbb{B})$ [is extremely useful for determining stationarity](02_autoregressive_models.md#causal-ar-models). In this section, we will explore another useful application of $\phi(\mathbb{B})$, namely converting a finite AR model into a MA($\infty$) consisting of an infinite series of noise (or shock) terms. This in turn will help us understand the autocovariance of AR processes and derive confidence intervals for predictions.

We've [already seen one example](#ar-models-in-backshift-notation) of converting to an infinite series of noise for AR(1) models

$$
\begin{equation}
\begin{split}
x_t&=(1 + \phi \mathbb{B} + \phi^2 \mathbb{B}^2 + \phi^3 \mathbb{B}^3 + \ldots)\,w_t\\
&= w_t + \phi w_{t-1} + \phi^2 w_{t-2}  + \phi^3 w_{t-3}  + \ldots
\end{split}
\end{equation}
$$

How might we go about finding weights, call them $\psi_j$'s with associated operator $\psi(\mathbb{B})$, for higher order AR($p$) processes? In other words, we want to find $\psi$ weights to satisfy the equation

$$
\begin{equation}
x_t = \psi(\mathbb{B})\,w_t
\end{equation}
$$ (psi-weight-def)

where we have defined $\psi_0\stackrel{\triangle}{=}1$.Given that an AR model is defined as

$$
\begin{equation}
w_t = \phi(\mathbb{B})\,x_t,
\end{equation}
$$ (ar-def-2)

we may combine Eqs. {eq}`psi-weight-def` and {eq}`ar-def-2` to arrive at

$$
\begin{equation}
    \begin{split}
         w_t &=\phi(\mathbb{B})x_t\\
        &=\phi(\mathbb{B})\psi(\mathbb{B})w_t\\ 
        &=(1 - \phi_1 \mathbb{B}-\phi_2 \mathbb{B}^2-\ldots-\phi_p \mathbb{B}^p)(1 + \psi_1 \mathbb{B} + \psi_2 \mathbb{B}^2 + \ldots)\\
        &= 1 + (\psi_1-\phi_1)\mathbb{B} + (\psi_2 -\phi_2 -\psi_1\phi_1) \mathbb{B}^2 +\ldots
    \end{split}
\end{equation}
$$ (psi-weight-calc)

Given that the left-hand side of Eq. {eq}`psi-weight-calc` does not have any backshifted terms, we conclude that the coefficients of each backshift operator must be zero, i.e.

$$
\begin{equation}
\begin{split}
\psi_0&=1\\
\psi_1-\phi_1&=0\\
\psi_1 &= \phi_1\\
\psi_2 -\phi_2 -\psi_1\phi_1 &= 0\\
\psi_2 -\phi_2 -\phi_1^2 &= 0 \qquad \text{substituting }\psi_1 = \phi_1\\ 
\psi_2 &=\phi_2 +\phi_1^2\\
\vdots
\end{split}
\end{equation}
$$ (deriving-psi-weights)

::::{tip} Problem
Derive the first three $\psi$ weights (i.e. $\psi_0$, $\psi_1$, and $\psi_2$) for the AR(2) model in Eq. {eq}`ar2-operator`.

:::{dropdown} Click to reveal solution
**Solution:** $\psi_0$ is always $1$. Using Eq. {eq}`deriving-psi-weights` we have $\psi_1=\phi_1=1.25$. Similarly, $\psi_2 =\phi_2 +\phi_1^2=-0.375+1.25^2=1.1875$.
:::
::::

::: {note} Exponential Decay of $\psi$ Weights
While not as obvious in Eq. {eq}`deriving-psi-weights` as it had been in Eq. {eq}`ar1-iteration`, the $\psi$ weights of any stationary AR process will decay exponentially. Logically, this should make sense as the lack of a unit root in stationary process means that it will eventually "forget" past noise and revert to its mean value. Consequently, we can often truncate $\psi(\mathbb{B})$ at around ten to twenty terms with minimal loss in accuracy.
:::

Fortunately, `statsmodels` performs the operations in Eq. {eq}`deriving-psi-weights` for us using the property `impulse_response`[^4]. Continuing with the example from above, add the following line to the code:

::: {code-cell} ipython3
print(f"psi weights 0-9: {ar2.impulse_response(10)}")
:::

Do the results agree with Eq. {eq}`deriving-psi-weights` applied in the problem above?
 
[^4]: The term "impulse response" comes from signal processing and denotes that fact that $\psi(\mathbb{B})$ dictates how noise, or an "impulse," decays with time. We will elaborate on this in the [next subsection](#where-do-ma-processes-arise-part-2).

### Where do MA Processes Arise? Part 2

The MA($\infty$) representation—referred to as the *impulse response* or *impulse response function* in disciplines such as signal processing—allows us to immediately determine how long a noise (or "impulse") continues to generate observations outside of the system's normal behavior. In the case of an AR(1) model who's [MA($\infty$) is simply](02_autoregressive_models.md)

$$
\sum_{j=0}^{\infty}\phi^j w_{t-j},
$$

it is straightforward in both the AR(1) and MA($\infty$) representations to determine that for, say, $\phi=\pm0.75$, the influence of an anomalous $w_t$ will decay to roughly $10\%$ of its initial value after $8$ timesteps, and roughly $1\%$ after $16$. For AR($p$) processes higher $p$ values, extracting this information directly from the AR representation becomes far more challenging. Representing the process in its MA($\infty$) form allows to quickly determine how a shock will decay by examining the $\psi$ weights. Moreover, for $p\geq2$, there is no guarantee that the $\psi$ weights will decay monotonically. An AR($p$) process with complex roots will exhibit correlations (and hence $\psi$ weights) that decay both exponentially **and sinusoidally**. The sinusoidal nature will result in $\psi$ weights that appear to reawaken at regular intervals and change the direction of their influence between positive and negative. Analyzing this decay of the $\psi$ weights allows us to avoid being surprised when we thought a shock had completely died off.



### $\psi$ Weight Representation

[](https://doi.org/10.1007/978-1-4419-0320-4) chapter 3.3 provides three broad methods to calculate the theoretical autocovariance and autocorrelation of AR models (and ARMA models in general). We will focus on the first here.

The first method relies on representing the model in the form of Eq. {eq}`psi-weight-def`. $\gamma(h)$ is then calculated in the same fashion as Eq. {eq}`ar1-acf-derivation`

$$
\begin{equation}
\begin{split}
\gamma(h) &= \text{Cov}(x_{t+h}, x_t)\\
&=\mathbb{E}[x_{t+h}x_t]\\
&=\mathbb{E}\Big[\Big(\sum_{j=0}^{\infty}\psi_j w_{t+h-j}\Big)\Big(\sum_{k=0}^{\infty} \psi_k w_{t-k}\Big)\Big]\\
&= \sigma_w^2 \sum_{i=0}^{\infty} \psi_i\,\psi_{i+h}
\end{split}
\end{equation}
$$ (psi-ar-gamma-0)

where we have used the fact that the noise is iid resulting in zero covariance for different noise terms. Unfortunately, AR($p$) models with $p\geq2$ do not have the same convenient [geometric series representation seen with an AR(1) model](#ar1-autocovariance). Nevertheless, Eq. {eq}`psi-ar-gamma-0` provides an elegant method for calculating the autocovariance, and hence the autocorrelation, of any stationary AR process—or, as we shall see, any stationary ARMA process. The fact that the $\psi$ weights decay exponentially results in it being possible to truncate Eq. {eq}`psi-ar-gamma-0` after say, thirty terms, with relatively little loss in accuracy.

### Recursion Relations

The second and third methods in [](https://doi.org/10.1007/978-1-4419-0320-4) rely on using recursion relations relating higher $h$ values to lower ones. We will not go into great depth regarding this method, but it is instructive to see how it might be applied to an AR(2) model. Let us begin with the AR(2) process $x_t = \phi_1 x_{t-1} + \phi_2 x_{t-2} + w_t$. We can multiply through by $x_{t-h}$ and take the expectation:

$$
\begin{equation}
\begin{split}
    x_t x_{t-h} &=  \phi_1 x_{t-1}x_{t-h} + \phi_2 x_{t-2}x_{t-h} + w_t x_{t-h}\\
    \mathbb{E}[x_t x_{t-h}] &=  \mathbb{E}[\phi_1 x_{t-1}x_{t-h}] + \mathbb{E}[\phi_2 x_{t-2}x_{t-h}] + \mathbb{E}[w_t x_{t-h}]\\
    \mathbb{E}[x_t x_{t-h}] &=  \phi_1\mathbb{E}[ x_{t-1}x_{t-h}] + \phi_2\mathbb{E}[x_{t-2}x_{t-h}] + \mathbb{E}[w_t x_{t-h}]\\
    \gamma(h) &= \phi_1\gamma(h-1) +\phi_2 \gamma(h-2) +  \mathbb{E}[w_t x_{t-h}].
\end{split}
\end{equation}
$$ (recursive-with-noise)

Provided that our AR(2) process is causal (stationary), we can rewrite the final term in Eq. {eq}`recursive-with-noise` as

$$
\begin{equation}
    \mathbb{E}[w_t x_{t-h}] = \mathbb{E}\Big[w_t\sum_{j=0}^{\infty}\psi_j w_{t-h-j}\Big]=0.
\end{equation}
$$ (ma-infty-zero)

Combining Eqs. {eq}`recursive-with-noise` and {eq}`ma-infty-zero` we arrive at the desired recursion relation

$$
\begin{equation}
    \gamma(h) = \phi_1\gamma(h-1) +\phi_2 \gamma(h-2)
\end{equation}
$$ (ar2-acovf-recursion)

Without additional information, we cannot further simplify in Eq. {eq}`ar2-acovf-recursion`. However, dividing through by $\gamma(0)$ generates a recursion relation for the autocorrelation

$$
\begin{equation}
    \rho(h) = \phi_1\rho(h-1) + \phi_2 \rho(h-2).
\end{equation}
$$

We know that $\rho(0)=1$, and we can evaluate $\rho(1)$ using Eq. {eq}`ar2-acovf-recursion`:

$$
\begin{equation}
    \begin{split}
        \gamma(1) &= \phi_1 \gamma(0) + \phi_2 \gamma(-1)\\
        &= \phi_1 \gamma(0) + \phi_2 \gamma(1)\\
        (1-\phi_2)\gamma(1) &= \phi_1 \gamma(0)\\
        \gamma(1) &= \frac{\phi_1}{1-\phi_2}\gamma(0)\\
        \rho(1) &= \frac{\phi_1}{1-\phi_2}\\
    \end{split}
\end{equation}
$$

Thus we can derive $\rho(2)$ as

$$
\begin{equation}
    \rho(2) = \frac{\phi_1^2}{1-\phi_2} + \phi_2,
\end{equation}
$$

and continue extending the recursion relation to higher values of $h$.