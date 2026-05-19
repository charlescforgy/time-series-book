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
	x_t = w_t + \theta_1 w_{t-1} + \theta_2 w_{t-2} +\ldots+\theta_q w_{t-q}.
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

By convention, the number of lags included in a given MA model is represented as $q$

### Moving Average Operator

As with AR models, we define the *moving average operator* as:

$$
\begin{equation}
	\theta(B) \stackrel{\triangle}= + \theta_1 \mathbb{B}+ \theta_2 \mathbb{B}^2 + \ldots + \theta_q \mathbb{B}^q,
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

Any $h>2$ will likewise have no noise terms in common and hence zero autocovariance. Eq. {eq}`ma1-acorf` is derived by dividing Eq. {eq}`ma1-h1` by Eq. {eq}`ma1-h1`:
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

From Eq. {eq}`ma_acovf`, we see that

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

## Non-uniqueness of MA Models}

While we theoretically understand MA processes as being the sum of noise terms, we do not directly observe the noise. We use functions such as the autocovariance and autocorrelation of observed values to derive the form of an MA process. MA models are, in general, not unique, for an MA(1) model, $\theta$ and $\frac{1}{\theta}$ yield the same $\rho$:

$$
\begin{equation}
	\begin{split}
	\rho(1) &= \frac{\theta}{1 + \theta^2}\\
	&= \frac{\theta}{1 + \theta^2}\times\frac{\frac{1}{\theta^2}}{\frac{1}{\theta^2}}\\
	&=  \frac{\frac{1}{\theta}}{(\frac{1}{\theta})^2 + 1}
	\end{split}
\end{equation}
$$ (ma-invert)
	

## Invertibility of MA Process

As seen in Eq. {eq}`ma-invert`, models with $\theta=5$ and $\theta=\frac{1}{5}$ yield the same $\rho$. Moreover, $\theta=5, \sigma_w^2 =1$ and $\theta=\frac{1}{5}, \sigma_w^2=25$ yield the same $\gamma$.We choose the model with $|\theta|<1$ as this model is *invertible*. In statsmodels, this is enforced by the argument `enforce_invertibility` with `default=True`. This allows the infinite AR representation

$$
\begin{equation}
	w_t = \sum_{j=0}^{\infty} (-\theta)^j x_{t-j}
\end{equation}
$$

where the negative sign arises from defining $\theta(\mathbb{B})=1+\theta \mathbb{B}=1-(-\theta) \mathbb{B}$.