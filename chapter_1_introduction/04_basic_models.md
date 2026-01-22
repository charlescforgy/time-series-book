# **3.4** Basic Time Series Models

## White Noise

A central model we will use throughout this textbook is **white noise**.

```{note} Whiteness of Noise
The reason for the description "white" will be analyzed in future chapters.
```

In the context of time series, white noise is defined as a sequence of random variables $w_t$ with mean $0$ and (finite) variance $\sigma_w^2$, denoted as[^1]:

$$w_t \sim wn(0, \sigma_w^2)$$

[^1]: The notation used in this book is explained in the [appendix](../appendices/notation.md).

In cases in which the mean is not zero, we can always subtract the mean to treat the remaining noise as white. We will often require the noise to be independent and identically distributed (iid). In particular, we will frequently use **Gaussian white noise**:

$$w_t \sim \mathcal{N}(0, \sigma_w^2)$$

```{note} Noise vs. Shocks
Disciplines such as econometrics use the term *shocks* instead of *noise*, often denoting shocks as $\epsilon_t$. The underlying models and statistics remain unchanged.
```

### Visualizing White Noise

```{figure} images/white_noise.png
---
width: 95%
name: white-noise-fig
---
Simulated white noise process.
```

```{figure} images/smoothed_white_noise.png
---
width: 95%
name: smoothed-noise-fig
---
Smoothed version of white noise.
```

The following code lets you play around with different values for the noise and distributions. Note that if you are reading this in GitHub pages you will not be able to run the cell, you'll either need to download the book or simpy copy and paste the code into a Jupyter notebook.

```{code-cell} ipython3

import numpy as np
import matplotlib.pyplot as plt

standard_deviation = 1.0 # Play around with this parameter!
white_noise = np.random.normal(loc=0.0, scale=standard_deviation, size=1000)
# We can also look at alternative distributions. Uncomment the following to use a Cauchy distribution instead.
# white_noise = np.random.standard_cauchy(size=1000)
plt.plot(np.arange(0, 1000, 1), white_noise)
plt.show()
```


## Moving Average

{ref}`white-noise-fig` and {ref}`smoothed-noise-fig` suggest one model for introducing temporal correlation in a series.

In a **moving average** (also known as a rolling average), each value is replaced by the average of that value and a selection of its neighbors.

### Simple Three-Value Moving Average

For example, a simple centered three-value moving average would define $v_t$ as:

$$v_t\stackrel{\triangle}{=}\frac{1}{3}(w_{t-1} + w_t + w_{t+1})$$
where $\stackrel{\triangle}{=}$ is read as "is defined as."

Even if the $w_t$'s are iid, the $v_t$'s will be **serially correlated**.

**Example:** Consider:
- $v_2=\frac{1}{3}(w_1+w_2+w_3)$
- $v_3=\frac{1}{3}(w_2+w_3+w_4)$

Both $v_2$ and $v_3$ depend on the values $w_2$ and $w_3$, creating correlation between consecutive observations despite $w_t$ being iid.

### Weighted Moving Average

Other weighting schemes are possible, for example:

$$v_t\stackrel{\triangle}{=}\frac{1}{10}w_{t-2}+\frac{2}{10}w_{t-1}+\frac{4}{10}w_{t}+\frac{2}{10}w_{t+1}+\frac{1}{10}w_{t+2}$$

This gives more weight to the central observation while still incorporating information from neighbors.

## Random Walk

Applying a moving average to uncorrelated noise is one plausible method for introducing temporal correlation. Another scenario is when each time step starts from the value of the previous point (e.g., a stock price that opens trading at yesterday's closing price).

### Basic Random Walk

This suggests a model of the form:

$$x_t = x_{t-1} + w_t$$

where $x_t$ is the value of the series $x$ at time $t$ and the $w_t$'s are iid white noise.

### Random Walk with Drift

If there is also an underlying trend (e.g., a stock's long-term price going up), we can add a constant drift term $\delta$:

$$x_t = \delta + x_{t-1} + w_t$$

### Apparent Trends

```{important} Spurious Trends
Random walks can very convincingly appear to have an underlying trend, even when none exists!
```

```{figure} images/simulated_random_walk.png
---
width: 95%
name: random-walk-fig
---
Simulated random walk with $w_t \sim \mathcal{N}(0, 1^2)$ showing apparent trends.
```

We can simulate a random walk starting with our white noise and using the `np.cumsum` function—if you don't understand why yet don't worry, we'll address it in later chapters. For now, this cell lets you get an idea of what random walks might look like. As before, you may need to copy and paste into a Jupyter notebook.

```{code-cell} ipython3

import numpy as np
import matplotlib.pyplot as plt

standard_deviation = 1.0 # Play around with this parameter!
white_noise = np.random.normal(loc=0.0, scale=standard_deviation, size=1000)
# We can also look at alternative distributions. Uncomment the following to use a Cauchy distribution instead.
# white_noise = np.random.standard_cauchy(size=1000)
plt.plot(np.arange(0, 1000, 1), np.cumsum(white_noise))
plt.show()
```

We will return to random walks and their cousins extensively throughout the book.

## Autoregression

The concept of a random walk can be generalized to **autoregressive processes**.

### Second Order Autoregression Example

Below we present a simulated autoregressive process generated by:

$$x_t = 1.5 x_{t-1} - 0.9 x_{t-2} + w_t$$

```{figure} images/ar2_sim.png
---
width: 95%
name: ar2-fig
---
Simulated AR(2) process.
```

Note the long-range patterns, despite the fact that the model can only "remember" two time steps into the past. Autoregressive models have surprising ability to capture complex dynamics.

## Signal and Noise

In many real-life scenarios, we consider our signal to be formed by the addition of white noise to an underlying process.

**Separating signal from noise** is a central focus in signal processing, with applications to:
- Radar systems
- Telecommunications
- Sound engineering
- Image processing
- And many other fields

### Example: Sinusoidal Signal with Noise

```{figure} images/sin_signal_noise.png
---
width: 95%
name: signal-noise-fig
---
Signal $2\sin\left(\frac{2\pi t}{20} + \frac{3}{5}\pi\right) + w_t$ for $w_t \sim \mathcal{N}(0,0^2)$, $
\mathcal{N}(0,1^2)$, and $\mathcal{N}(0, 5^2)$.
```

As the variance of the noise increases, it becomes progressively more difficult to recover the underlying signal. Much of time series analysis is concerned with developing methods to extract meaningful patterns from noisy observations.

:::{tip} Problem
One of the best ways to become comfortable with time series and avoid being misled by apparent patterns is to simulate random series and plot the output. Use the code above to plot 10 different time series. Do they appear to exhibit a pattern? What happens if you use a Cauchy distribution instead of a Gaussian?
:::