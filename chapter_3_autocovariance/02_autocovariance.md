# Autocovariance and Autocorrelation

## Autocovariance

As discussed in [the first chapter](../chapter_1_introduction/02_characteristics.md#serial-correlation), serial correlation of time series is both one the most challenging and most valuable aspect of time series. *Autocovariance* allows us to exploit serial correlation to address questions such as:

- Is a high market close on one day indicative of a lower close the following day?
- How long does a heat wave continue to skew temperatures above the norm?
- Given higher sunspot activity this year, what year(s) in the future should we expect a repeat of this activity?

To give a concrete example, if we wish to know how strongly today's highest temperature influences tomorrow's we may take the covariance of the series $(T_{0}, T_{1}, ...,T_{n-1})$ and $(T_{1}, T_{2}, ...,T_{n})$, i.e.

$$
\begin{equation}
\text{Cov}\Big(\sum_{i=0}^{n-1} T_i, \sum_{j=1}^{n} T_j \Big).
\end{equation}
$$ (autocovariance-temp)

A high positive value for Eq. {eq}`autocovariance-temp` tells us that higher (lower) temperatures today are indicative of higher (lower) temperatures tomorrow. While implausible for weather, in general many time series exhibit negative correlation, in which case a higher (lower) value at one time step is indicative of a lower (higher) value in the next.

We can naturally extent Eq. {eq}`autocovariance-temp` to larger time lags to ask how long an anomalous temperature continues to skew the daily high. We will see in subsequent chapters that it is very common for time series to obey an exponentially decaying autocovariance.

```{warning}
The value that we have defined as autocovariance is often referred to instead as *autocorrelation* in the fields of electrical engineering and signal processing. Our definition is consistent with the common usage in statistics.
```

### Notation

Following the notation used in sources such as [](https://doi.org/10.1007/978-3-031-70584-7) and [](https://doi.org/10.1007/978-3-319-29854-2), we will use the notation $\gamma_{x}(s, t)$ to denote the autocovariance at time lags $s$ and $t$ for time series $x$:

$$
\begin{equation}
	\begin{split}
	    \gamma_{x}(s, t) &\stackrel{\triangle}= \mathbb{E}[(x_{s}-\mu_{s})(x_{t}-\mu_{t})]\\
				 &=\mathbb{E}[x_{s}x_{t}]-\mu_{s}\mu_{t}\\
	\end{split}
\end{equation}
$$
Note that $\gamma_x(s, t)=\gamma_x(t, s)$. 

In general, we will drop the $x$ subscript when it is obvious which time series we are referring to and only write $\gamma(s, t)$. For an arbitrarily large (or infinite) observation window, $\mu_{s}=\mu_{t}$ as both series share almost all the same observations, leading to the simplification

$$
\mathbb{E}[x_{s}x_{t}]-\mu^2\\
$$
where we have dropped the subscript on $\mu$.

## Mean and Autocovariance of Moving Average

Recall from [the first chapter](../chapter_1_introduction/04_basic_models.md#white-noise) that a white noise process is defined as a mean zero process with finite variance

$$
w_t \sim wn(0, \sigma_w^2).
$$

A summation of multiple values of $w_t, w_{t+1}, w_{t+2},\ldots$ will still have a mean of zero

$$
\mathbb{E}[\ldots + w_{t-1}+ w_{t}+w_{t+1}+\ldots] = \dots+\mathbb{E}[w_{t-1}]+\mathbb{E}[w_{t}] + \mathbb{E}[w_{t+1}]+\ldots=0. 
$$

Consider the moving average

$$v_t = \frac{1}{3}(w_{t-1}+w_t+w_{t+1}).$$

$v_t$ has a mean of zero, but the autocovariance will depend on the difference between $s$ and $t$:
$$
\begin{equation}
	    \gamma_v(s, t)=\begin{cases}
		\frac{3}{9}\sigma_w^2, & s=t\\
		\frac{2}{9}\sigma_w^2, & |s-t|=1\\
		\frac{1}{9}\sigma_w^2, & |s-t|=2\\
		0, & \text{otherwise.}\\
	\end{cases}
\end{equation}
$$ (moving-average-acf)

Note that **even if all instances of the time series are iid,** averaging will still introduce serial correlation.

**Problem:** Prove Eq. {eq}`moving-average-acf`. Hint, since the $w_t$'s are iid $\text{Cov}(w_i, w_j)=\delta_{ij}\sigma_w^2$, where $\delta_{ij}$ is defined as 1 if $i=j$ and 0 otherwise.

```{dropdown} Click to reveal solution
**Solution:** Let's go through the four cases in order:
$$
\begin{split}
\gamma_v(t,t) &= \text{Cov}\big(\frac{1}{3}(w_{t-1}+ w_{t}+w_{t+1}), \frac{1}{3}(w_{t-1}+ w_{t}+w_{t+1})\big)\\
&= \frac{1}{9}\Big[\text{Cov}(w_{t-1}, w_{t-1}) + \text{Cov}(w_{t-1}, w_{t}) + \ldots + \text{Cov}(w_{t+1}, w_{t+1})\Big]\\
&= \frac{1}{9}\Big[\text{Cov}(w_{t-1}, w_{t-1}) + \text{Cov}(w_{t}, w_{t}) + \text{Cov}(w_{t+1}, w_{t+1})\Big]\\
&= \frac{1}{9}\Big[\mathbb{V}(w_{t-1}) + \mathbb{V}(w_{t})+\mathbb{V}(w_{t+1})\big]\\
&= \frac{3}{9}\sigma_w^2
\end{split}
$$
where we have exploited the fact that the covariance between different instances of $w_t$ is 0 to move between the second and third lines.

$$
\begin{split}
\gamma_v(t,t+1) &= \text{Cov}\big(\frac{1}{3}(w_{t-1}+ w_{t}+w_{t+1}), \frac{1}{3}(w_{t}+ w_{t+1}+w_{t+2})\big)\\
&= \frac{1}{9}\Big[\text{Cov}(w_{t}, w_{t}) + \text{Cov}(w_{t+1}, w_{t+1})\Big]\\
&= \frac{1}{9}\Big[\mathbb{V}(w_{t})+\mathbb{V}(w_{t+1})\big]\\
&= \frac{2}{9}\sigma_w^2
\end{split}
$$

$$
\begin{split}
\gamma_v(t,t+2) &= \text{Cov}\big(\frac{1}{3}(w_{t-1}+ w_{t}+w_{t+1}), \frac{1}{3}(w_{t+1}+ w_{t+2}+w_{t+3})\big)\\
&= \frac{1}{9}\text{Cov}(w_{t+1}, w_{t+1})\\
&= \frac{1}{9}\mathbb{V}(w_{t+1})\\
&= \frac{1}{9}\sigma_w^2
\end{split}
$$

Finally, note that following the above derivations, if $|s-t|\geq3$, any two instances of $v_t$ and $v_s$ will have zero terms in common, leading to 0 autocovariance.
```

## Mean and Autocovariance of Random Walk