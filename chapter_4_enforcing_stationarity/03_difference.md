# 4.3 Differencing and the Backshift Operator

## Random Walk

As discussed, a random walk [has a non-constant variance](../chapter_3_autocovariance/02_autocovariance.md#mean-and-autocovariance-of-random-walk). Since $\gamma(0)$ is not constant, a random walk (even without drift) is not stationary. How might we go about isolating a stationary time series from a random walk? Detrending will remove any drift thereby stabilizing the mean, but it will not help with the non-constant variance. Instead, we rely on the process of *differencing*.

Differencing is defined as taking the difference between values. For example, the first difference of a random walk without drift is given by

$$
z_t = x_{t} - x_{t-1}.
$$

Note that (starting our time series with $x_0=0$)

$$
\begin{equation}
\begin{split}
z_1 &= x_1 - x_0\\ 
&= (x_0+w_1) - 0\\
&= w_1\\
z_2 &= x_2-x_1\\
&= (x_1+w_2) - x_1\\
&= w_2\\
&\ldots
\end{split}
\end{equation}
$$ (random-walk-no-drift-diff)

Thus, the first difference of a random walk without drift is white noise and hence stationary. What about a random walk with drift? Starting this time with $x_0=\delta$

$$
\begin{equation}
\begin{split}
z_1 &= x_1 - x_0\\ 
&= (\delta + x_0+w_1) - \delta\\
&= (2\,\delta +w_1) - \delta\\
&= \delta + w_1\\
z_2 &= x_2-x_1\\
&= (\delta + x_1+w_2) - x_1\\
&= \delta + w_2\\
&\ldots
\end{split}
\end{equation}
$$ (random-walk-drift-diff)

Since $\delta$ is constant, $\mathbb{E}[z_t]=\delta$ is also constant. Similarly, the addition of $\delta$ [does not change the covariance $\gamma(h)$](../chapter_3_autocovariance/02_autocovariance.md#random-walk-variance), so the first difference of a random walk with drift is also stationary.

:::{note} Forecasting Random Walk
For a pure random walk, it is impossible to make a better statistical prediction that predicting that the next value will be the same as the current value plus any drift when applicable.
:::

## Difference Stationary Process

For pure random walk processes, Eq.s {eq}`random-walk-no-drift-diff` and {eq}`random-walk-drift-diff` represent the limit of our analysis. However, very often we will encounter a *difference stationary process* defined again as

$$
\begin{equation}
x_t = \mu_t + y_t
\end{equation}
$$ (difference-stationary-def)

where in this case $\mu_t$ is a random walk process[^1]. Taking the first difference, we have

[^1]: We will discover other scenarios of difference stationary process where $\mu_t$ is not a simple random walk, but is a process possessing a *unit root*. We will defer discussion of these cases until after we have covered ARMA processes. For the time being, you can think of the concept of unit root as referring to random walks.

$$
\begin{equation}
\begin{split}
x_t-x_{t-1} &= (\mu_t+y_t) - (\mu_{t-1}+y_{t-1})\\
&= (\delta +\mu_{t-1} + w_t+y_t) - (\mu_{t-1} + y_{t-1})\\
&= \delta + y_t - y_{t-1} + w_t
\end{split}
\end{equation}
$$

If $y_t$ is stationary, the first difference $v_t=y_t-y_{t-1}$ must be as well:

$$
\mathbb{E}[v_t] = \mathbb{E}[y_t-y_{t-1}]=\mathbb{E}[y_t]-\mathbb{E}[y_{t-1}]=\mu_y-\mu_y=0
$$

and

$$
\begin{equation}
\begin{split}
\gamma_v(h) &= \text{Cov}(v_{t+h}, v_t)\\
&= \text{Cov}(y_{t+h}-y_{t+h-1}, y_t-y_{t-1})\\
&= 2\,\gamma_y(h) - \gamma_y(h-1)-\gamma_y(h+1)
\end{split}
\end{equation}
$$

::::{tip} Problem
Prove that the fact that $v_t$ is stationary also demonstrates that $z_t=x_t-x_{t-1}$ must also be stationary.

:::{dropdown} Click to reveal solution
**Solution:** We have already show that $\mathbb{E}[y_t-y_{t-1}]=0$. Consequently,

$$
\mathbb{E}[x_t-x_{t-1}] = \mathbb{E}[\delta + y_t - y_{t-1} + w_t] = \delta
$$

which is clearly constant for all $t$. Similarly, (recognizing that the constant $\delta$ has no effect on the covariance) $\gamma_z(h)$ is given by

$$
\begin{equation}
\begin{split}
&\text{Cov}(y_{t+h} - y_{t+h-1} + w_{t+h}, y_t - y_{t-1} + w_t)\\
&=\text{Cov}(y_{t+h} - y_{t+h-1}, y_t - y_{t-1})+\text{Cov}(w_{t+h}, w_t)\\
&= 2\,\gamma_y(h) - \gamma_y(h-1)-\gamma_y(h+1) + \gamma_w(h)\\
&= 2\,\gamma_y(h) - \gamma_y(h-1)-\gamma_y(h+1) + \delta_{h,0}\sigma_w^2
\end{split}
\end{equation}
$$

where we have assumed iid $w_t$.
:::
::::