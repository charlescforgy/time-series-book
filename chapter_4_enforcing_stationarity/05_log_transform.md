# 4.5 Increasing Variance and the Log Transform

Most of the time series we've seen so far have had constant variance across time[^1]. In some scenarios, particularly those involving exponential growth, variance may increase concomitantly with the value. For example, as overall air travel increases the absolute difference between busier and less busy seasons will also increase (even if the relative difference is constant). Such time series are non-stationary for at least two (and often three) reasons:

[^1]: A random walk is an important exception. Random walks [have a linearly increasing variance with respect to time.](../chapter_3_autocovariance/02_autocovariance.md#random-walk-variance)

1. The mean is non-constant over time.
2. The variance $\gamma(0)$ is non-constant over time.
3. Some examples such as air travel may also be non-stationary due to seasonal effects.

## Apple Mac Sales

:::{figure} images/mac_sales_raw.png
---
width: 95%
name: mac-sales-raw
---
Quarterly sales figures for Apple Mac computers from [](https://github.com/kjhealy/apple).
:::

{ref}`mac-sales-raw` appears to be non-stationary for all three reasons listed above. The mean shows a steady upward trend and the volumes appear to exhibit a seasonal effect with higher sales in the third and fourth quarters and lower sales in the first and second. As the size of the seasonal effect appears to depend on the overall trend, the variance also increases with time.

Let's first address the increasing variance by replacing the raw values with their logarithms:

:::{figure} images/mac_sales_log.png
---
width: 95%
name: mac-sales-log
---
Logarithm of quarterly sales figures for Apple Mac computers from [](https://github.com/kjhealy/apple).
:::

{ref}`mac-sales-log` is beginning to look better—the variance appears to be roughly constant across time. We can address both the trend and seasonality by taking the first and fourth differences of the log values, i.e.

$$
\begin{equation}
z_t \overset{\triangle}{=} \nabla_4\,\nabla\big(\log{(x_t)}\big)
\end{equation}
$$ (log-diff)

:::{figure} images/mac_sales_log.png
---
width: 95%
name: mac-sales-log-diff
---
First and fourth differences of logarithm of quarterly sales figures for Apple Mac computers from [](https://github.com/kjhealy/apple).
:::

{ref}`mac-sales-log-diff` certainly looks stationary upon visual inspection. After we've covered autoregressive processes we will revisit this example with statistical tests designed to help determine stationarity.

::::{tip} Problem
In Eq. {eq}`log-diff` we applied the first difference at lag $1$ followed by the first difference at lag $4$. Would it have made a difference if we'd switched the order and instead used $\nabla\,\nabla_4\big(\log{(x_t)}\big)$?

:::{dropdown} Click to reveal solution
**Solution:** Recall the the difference operator can be written in backshift notation as

$$
\nabla = 1-\mathbb{B}
$$
and
$$
\nabla_4 = 1-\mathbb{B}^4.
$$

Multiplying these out, we get

$$
\begin{align*}
\nabla_4\,\nabla &= (1-\mathbb{B}^4)(1-\mathbb{B})\\
&= 1-\mathbb{B}- \mathbb{B}^4 + \mathbb{B}^5\\
&=(1-\mathbb{B})(1-\mathbb{B}^4)\\
&= \nabla\,\nabla_4
\end{align*}
$$
Thus, we see that the two operators commute in this scenario.
:::
::::

### Log Difference

In Eq. {eq}`log-diff` we used the first (and fourth) difference of the logarithm of the sales figures. This concept closely relates to the *return*, a unitless quantity defined as the difference in value between successive time steps divided by the previous time step, i.e.

$$
\begin{equation}
r_t \overset{\triangle}{=} \frac{x_t-x_{t-1}}{x_{t-1}}.
\end{equation}
$$ (return-def)

To see this relation, let us rewrite Eq. {eq}`return-def` as

$$
\frac{x_t}{x_{t-1}} = 1+r_t.
$$

We know have

$$
\begin{equation}
\begin{split}
\nabla \log{(x_t)} &= \log{(x_t)}-\log{(x_{t-1})}\\
&= \log{\Big(\frac{x_t}{x_{t-1}}\Big)}\\
&= \log(1+r_t)\\
&\approx r_t
\end{split}
\end{equation}
$$

where we have used the Taylor expansion

$$
\log{(1+r_t)} = r_t -\frac{r_t^2}{2}+\dots
$$

Under the assumption of small $r_t$, quadratic and higher order terms will be negligible. {ref}`log-returns` demonstrates that the two functions are essentially identical for returns with an absolute value less than about $20\%$.

:::{figure} images/log_r_vs_r.png
---
width: 95%
name: log-returns
---
Linear slope $y=r_t$ (gray) and $y=\log{(1+r_t)}$ (blue) for $r_t\in[-0.5, 0.5]$.
:::