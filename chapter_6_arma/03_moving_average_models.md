# 6.3 Moving Average Models (Under Construction)

## Moving Average Models

Recall that we might construct [a three-point moving average as](../chapter_3_autocovariance/02_autocovariance.md#mean-and-autocovariance-of-moving-average)

$$
v_t = \frac{1}{3}(w_{t-1} + w_t + w_{t+1}),
$$

which will result in $v_t$ having the same expectation value as $w_t$, but will introduce autocorrelation at lags $h=1,2$.

Moving average (MA) models of order $q>1$ generalize this idea with the model

$$
\begin{equation}
	x_t = w_t + \theta_1 w_{t-1} + \theta_2 w_{t-2} +\ldots+\theta_q w_{t-q}, \qquad \theta_q\neq0.
\end{equation}
$$ (ma-def)
            
By convention, the number of lags included in a given MA model is represented as $q$, and the overall model is referred to as an MA($q$) model. Unlike a true moving average, MA models do not require the $\theta$'s to sum to unity, or even to be positive.

:::: {tip} Problem
Why doesn't an MA model use $w_{t+1}$?

:::{dropdown} Click to reveal solution
**Solution:** MA models (and ARMA in general) are designed to be used both for time series analysis and time series forecasting. Clearly, a model that needs to know the future in order to forecast it is not a particularly useful tool.
:::
::::

::: {note} Moving Average Terminology
We are arguably abusing the terminology by referring to Eq. {eq}`ma-def` as a "moving average" without ever requiring the $\theta$'s to actually average $w_t$. While MA models might have been better named something like "noise convolution" models, the MA terminology has been universally adopted and we will use that convention here.
:::

### Parameter Estimation

At this point, you may well be wondering how we could ever expect to estimate the $\theta$'s in Eq. {eq}`ma-def` in real life. AR models can be calculated by lining up past observations and running a least squares regression (though in practice related methods such as Yule-Walker are often favored). In contrast, how are we supposed to know the $w_t$'s necessary to estimate an MA model? We observed the values $x_t$, not the noise $w_t$!

To answer this, let us take a step back and imagine we knew for a fact that a given process was described by the MA(1) model

$$
x_t = w_t + \theta w_{t-1}.
$$

In this case, our estimate of $\widehat{w}_t$ would be given by $x_t-\theta \widehat{w}_{t-1}$. By the same token we would estimate $\widehat{w}_{t-1}=x_{t-1}-\theta \widehat{w}_{t-2}$, $\widehat{w}_{t-2}= x_{t-2}-\theta \widehat{w}_{t-3}$, etc. We can flip this around the use *maximum likelihood estimation* (MLE) to estimate the value $\hat{\theta}$ most consistent with our observations. This procedure can be extended to higher level MA($q$) processes using the same logic. Sources such as [](https://doi.org/10.1007/978-3-031-70584-7) chapter 3.5 and [](https://doi.org/10.1007/978-1-4419-0320-4) chapter 5.1-5.2 go into some detail regarding methods such as Newton-Raphson, Gauss-Newton, and the innovations algorithm. Understanding the exact algorithms used for implementing MLE for MA (and ARMA) models is not as important for a practicing data scientist as **understanding that MLE is being used**. The use of MLE has two important ramifications you should be aware of:

1. MA models (and the MA component of ARMA models) are generally less numerically stable than pure AR models, resulting in them potentially being less reliable.
2. MA models are less interpretable than AR models. An AR model is purely determined by previous observations and its meanings and ramifications can be easily explained to clients. In contrast, an MA model relies on inferring an unseen noise term which can be challenging to explain to a less technical audience.

For these reasons, AR models are often preferred over MA models when they give comparable results with a similar number of parameters[^1].

[^1]: We will explore methods for converting between ARMA, pure AR, and pure MA models in [section 4](04_arma.md).

### Moving Average Operator

As with AR models, we define the *moving average operator* as:

$$
\begin{equation}
	\theta(B) \stackrel{\triangle}=1 + \theta_1 \mathbb{B}+ \theta_2 \mathbb{B}^2 + \ldots + \theta_q \mathbb{B}^q,
\end{equation}
$$ (moving-average-operator-def)

allowing us to express Eq. {eq}`ma-def` as

$$
\begin{equation}
    \begin{split}
        x_t &= w_t + \theta_1 w_{t-1} + \theta_2 w_{t-2} +\ldots+\theta_q w_{t-q}\\
        &= \theta(\mathbb{B})\,w_t.
    \end{split}
\end{equation}
$$

Note the change in sign convention from the autoregressive operator.

## MA(1) Mean & Autocovariance

Provided that $w_t$ has a finite second moment, all MA processes will be stationary. The mean of a pure MA process will always be zero. The autocovariance is calculated by matching $w_t$ terms, for an MA(1) process we have:

$$
\begin{equation}
\gamma(h) = \begin{cases} 
		(1+\theta^2)\,\sigma_w^2 & h=0\\
		\theta \sigma_w^2 & h=1\\
		0 & h>1.\\
		\end{cases}
\end{equation}
$$ (ma1-acovf)

The autocorrelation for an MA(1) process is:

$$
\begin{equation}
	\rho(h) = \begin{cases}
	\frac{\theta}{1+\theta^2} & h=1\\
	0 & h>1\\
	\end{cases}
\end{equation}
$$ (ma1-acorf)

:::{important} Sudden Cutoff of Autocorrelation
A signature characteristic of an MA process is having statistically significant autocorrelation up to lag $q$ followed by an immediate drop to statistical insignificance. This draws a strong contrast with AR processes, which exhibit exponentially decaying autocovariance.
:::

:::: {tip} Problem
Derive Eqs. {eq}`ma1-acovf` and {eq}`ma1-acorf`.

:::{dropdown} Click to reveal solution
**Solution:** This follows the same pattern [derived for the autocovariance of a true moving average](../chapter_3_autocovariance/02_autocovariance.md#mean-and-autocovariance-of-moving-average). Going through the cases in order and noting that $\text{Cov}(w_i, w_j)=\mathbb{E}[w_i w_j]=\sigma_w^2\delta_{ij}$:

$$
\begin{equation}
    \begin{split}
        \gamma(0) &= \text{Cov}(x_t, x_t)\\
        &=\mathbb{E}[x_{t}x_t]\\
        &=\mathbb{E}[(w_t+\theta w_{t-1})(w_t+\theta w_{t-1})]\\
        &=\mathbb{E}[w_t w_t] + \theta^2\,\mathbb{E}[w_{t-1}w_{t-1}]\\
        &=\sigma_w^2+\theta^2\,\sigma_w^2\\\
        &=(1+\theta^2)\,\sigma_w^2,
    \end{split}
\end{equation}
$$ (ma1-h0)

$$
\begin{equation}
    \begin{split}
        \gamma(1) &= \text{Cov}(x_{t+1}, x_{t})\\
        &=\mathbb{E}[x_{t+1}x_t]\\
        &=\mathbb{E}[(w_{t+1}+\theta w_{t})(w_t+\theta w_{t-1})]\\
        &=\theta\,\mathbb{E}[w_{t}w_{t}]\\
        &=\theta\, \sigma_w^2,
    \end{split}
\end{equation}
$$ (ma1-h1)

$$
\begin{equation}
    \begin{split}
        \gamma(2) &= \text{Cov}(x_{t+2}, x_{t})\\
        &=\mathbb{E}[x_{t+2}x_t]\\
        &=\mathbb{E}[(w_{t+2}+\theta w_{t+1})(w_t+\theta w_{t-1})]\\
        &=0.
    \end{split}
\end{equation}
$$

Any $h>2$ will likewise have no noise terms in common and hence zero autocovariance. Eq. {eq}`ma1-acorf` is derived by dividing Eq. {eq}`ma1-h1` by Eq. {eq}`ma1-h0`:

$$
\begin{equation}
    \begin{split}
        \rho(1) &= \frac{\theta\, \sigma_w^2}{(1+\theta^2)\,\sigma_w^2}\\
        &= \frac{\theta}{1+\theta^2}
    \end{split}
\end{equation}
$$

:::
::::
	
### Sign of $\theta$

Similar to what we observed with [AR models in the previous section](02_autoregressive_models.md), an MA($1$) model with $\theta<0$ will introduce serial negative correlation, resulting in a jagged time series. Unlike AR models, a negative $\theta$ will not introduce oscillations in the autocorrelation for any lag greater than $q$. The following tool helps demonstrate the effects of different $\theta$ values for MA($1$) and MA($2$) processes.

<iframe src="/time-series-book/ma_process_demo.html" width="100%" height="520" frameborder="0" scrolling="no"></iframe>

If the above fails to render correctly in your browser you can also open the demo as a new browser window using the `Open Demo in a New Tab ↗` button at the top of the frame. Note that you may need to enable popups for this to work.


	
### Autocovariance of Higher Order MA Processes

An MA($q$) process can be written as $x_t = \sum_{j=0}^{q}\theta_j w_{t-j}$, where $\theta_0=1$. The mean of an MA process is $\mathbb{E}\Big[\sum_{j=0}^{q}\theta_j w_{t-j}\Big]=\sum_{j=0}^{q}\theta_j \mathbb{E}[w_{t-j}]=0$.The autocovariance $\gamma(h)$ will depend on the number of $w_t$ terms in common between $x_{t}$ and $x_{t+h}$:

$$
\begin{equation}
	\begin{split}
		\gamma(h) &= \text{Cov}{(x_{t+h}, x_t)}\\
		&= \text{Cov}{\Big(\sum_{j=0}^{q} \theta_j w_{t+h-j}, \sum_{k=0}^{q}\theta_k w_{t-k}\Big)}\\
		&=\begin{cases}
		\sigma_w^2 \sum_{j=0}^{q-h}\theta_j\theta_{j+h}, & 0\leq h\leq q\\
		0 & h>q.
		\end{cases}
	\end{split}
\end{equation}
$$ (ma-acovf)

	
### Autocorrelation of Higher Order MA Processes

From Eq. {eq}`ma-acovf`, we see that

$$
\begin{equation}
	\gamma(0) = \sigma_w^2 (1 + \theta_1^2 + \ldots +\theta_q^2),
\end{equation}
$$

thus giving us the autocorrelation

$$
\begin{equation}
	\begin{split}
	\rho(h) &= \frac{\sigma_w^2\sum_{j=0}^{q-h}\theta_j\theta_{j+h}}{\sigma_w^2 (1 + \theta_1^2 + \ldots +\theta_q^2)}\\
	&= \begin{cases}
    \frac{\sum_{j=0}^{q-h}\theta_j\theta_{j+h}}{1 + \theta_1^2 + \ldots +\theta_q^2}, & 1\leq h\leq q\\
	0 & h>q.
	\end{cases} 
	\end{split}
\end{equation}
$$

When analyzing time series in real-life, observing a $\gamma(h)$ that is statistically significant for $q$ terms and then drops to insignificance is a strong indicator of an MA($q$) process.

## Non-uniqueness of MA Models

While we theoretically understand MA processes as being the sum of noise terms, we do not directly observe the noise. We use functions such as the autocovariance and autocorrelation of observed values to derive the form of an MA process. MA models are, in general, not unique. Let us examine an MA(1) model (higher order $q$'s are derived analogously); for an MA(1) model $\theta$ and $\frac{1}{\theta}$ yield the same $\rho$:

$$
\begin{equation}
	\begin{split}
	\rho(1) &= \frac{\theta}{1 + \theta^2}\\
	&= \frac{\theta}{1 + \theta^2}\times\frac{\frac{1}{\theta^2}}{\frac{1}{\theta^2}}\\
	&=  \frac{\frac{1}{\theta}}{(\frac{1}{\theta})^2 + 1}
	\end{split}
\end{equation}
$$ (ma-invert)

Using the autocovariance $\gamma(h)$ won't help, either. To demonstrate this for an MA(1) model, let us return to Eq. {eq}`ma1-acovf`, where we found that for an MA(1) process

$$
\begin{equation}
\gamma(h) = \begin{cases} 
		(1+\theta^2)\,\sigma_w^2 & h=0\\
		\theta \sigma_w^2 & h=1\\
		0 & h>1.\\
		\end{cases}
\end{equation}
$$

To use an example from [](https://doi.org/10.1007/978-3-031-70584-7), consider an MA(1) model $\sigma_w^2=1$ and $\theta=5$. In this case, $\gamma(0)=1+5^2=26$, and $\gamma(1)=5$. Now consider a model with $\sigma_w^2=25$ and $\theta=\frac{1}{5}$. Here too, $\gamma(0)=(1+\frac{1}{5^2})25=\big(\frac{26}{25}\big)25=26$, and $\gamma(1)=\big(\frac{1}{5}\big)25=5$.

If we could somehow directly observe the noise $w_t$, we would be able to differentiate the models by looking at the variance of the noise. Unfortunately, we can only infer the variance of $w_t$ from looking at $\gamma(h)$. We thus see that **two distinct MA(1) models can equally well describe the same underlying process with no way to distinguish the "true" model** (though I would caution any practicing data scientist to be wary of conflating reality itself with a model of reality such as an MA model).

## Invertibility of MA Process

As seen in Eq. {eq}`ma-invert`, models with $\theta=5$ and $\theta=\frac{1}{5}$ yield the same $\rho$. Moreover, $\theta=5, \sigma_w^2 =1$ and $\theta=\frac{1}{5}, \sigma_w^2=25$ yield the same $\gamma$.We choose the model with $|\theta|<1$ as this model is *invertible*. In statsmodels, this is enforced by the argument `enforce_invertibility` with `default=True`. This allows the infinite AR representation

$$
\begin{equation}
\begin{split}
	w_t &= \theta^{-1}(\mathbb{B})\,x_t\\
	&= \frac{1}{1+\theta \mathbb{B}}\,x_t\\
	&= \sum_{j=0}^{\infty} (-\theta)^j x_{t-j}\\
\end{split}
\end{equation}
$$ (ar2ma-infinity)

where the negative sign arises from defining $\theta(\mathbb{B})=1+\theta \mathbb{B}=1-(-\theta) \mathbb{B}$.

:::: {tip} Problem
How might a non-invertible MA(1) model arise?

:::{dropdown} Click to reveal solution
**Solution:** An MA(1) model will be non-invertible if $\theta=\frac{1}{\theta}$, i.e. if $\theta=\pm1$. This frequently arises as the result of excessive [differencing when trying to make a model stationary](../chapter_4_enforcing_stationarity/03_difference.md). To give a basic example, consider the MA(1) model of white noise

$$
x_t=w_t.
$$

If we apply differencing to white noise, we get the series

$$
\nabla x_t = w_t-w_{t-1},
$$

which is a non-invertible MA(1) model. This reinforces why we should limit differencing to cases where it is [strictly necessary](../chapter_4_enforcing_stationarity/03_difference.md#differencing-vs-detrending).
:::
::::

### AR($\infty$) and MA($\infty$) Representations

Invertibility plays the same role for MA models that causality (or stationarity) does for AR models. Indeed, we could repeat most all of the [analysis performed for AR models](02_autoregressive_models.md#causal-ar-models) simply by renaming the variables. Swapping $x_t$ and $w_t$ and renaming $\phi_i$ to $-\theta_i$ will allow us to transform an MA($1$) model into an AR($\infty$) model[^1] (as explicitly derived in Eq. {eq}`ar2ma-infinity`). Thus, we arrive at the conclusion that any **causal** AR($1$) can be represented as an MA($\infty$), and any **invertible** MA($1$) model can be represented as an AR($\infty$). In the [next section on ARMA](./04_arma.md) we will develop tools to transform any causal AR($p$) into an MA($\infty$), and likewise to convert any invertible MA($q$) model into an AR($\infty$) model.

[^1]: The [function `arma_impulse_response` in `statsmodels`](https://www.statsmodels.org/dev/_modules/statsmodels/tsa/arima_process.html#arma_impulse_response) actually does convert to either AR($\infty$) or MA($\infty$) simply by switching which set of variables are used as $\phi_i$ and which are used as $\theta_i$.


## Where do MA Processes Arise?

Pure MA($q$) models with a finite $q$ are sometimes referred to as "short-memory" models to contrast them with the longer memory of AR models. Pure MA models are somewhat less prevalent but do arise in scenarios such as items with a shelf-life which inherently have a short "memory." As an example, a "shock" in the prices of dairy will quickly die off as we approach the end of the products' shelf-lives. A glut in production will become irrelevant once the extra products expire, whereas a scarcity of production will rapidly reset as future purchases return to their baseline (as there is no need to refill long term stockpiles)[^2].

[^2]: This is something of an oversimplification as in the United States [the government maintains long-term cold cheese storage](https://www.ams.usda.gov/mnreports/dymcoldstor_cheese.pdf), in part to help smooth out supply chain shocks by absorbing excess production and providing relief during scenarios such as disaster relief. Nevertheless, our model is reasonable for local markets that may not participate in government cheese programs.

Arguably, however, MA models truly shine in the context of analyzing AR (or ARMA) models in their MA($\infty$) representations. We will return to this topic once we've laid additional foundation in the [next section](04_arma.md) covering ARMA.