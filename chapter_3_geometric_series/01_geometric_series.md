# Geometric Series

Geometric series appear frequently in time series analysis, particularly when working with ARMA models and spectral analysis. Understanding their properties is essential for many derivations in this course.

## Finite Geometric Series

Let $S = x + x^{2} + x^{3} + \ldots + x^{N} = \sum_{n=1}^{N}x^{n}$. This is known as a **geometric progression**.

Now consider $xS$:

$$xS = x^{2} + x^{3} + x^{4} + \ldots + x^{N+1} = \sum_{n=2}^{N+1}x^{n}$$

Subtracting these:

\begin{align}
(1-x)S &= S - xS\\
&= (x + x^{2} + x^{3} + \ldots + x^{N}) - (x^{2} + x^{3} + x^{4} + \ldots + x^{N+1})\\
&= x - x^{N+1}\\
&= x(1-x^{N})
\end{align}

Therefore:

$$S = x\frac{1-x^{N}}{1-x} \quad \text{for } x \neq 1$$

```{note}
When we instead sum from $n=0$, the formula becomes $\frac{1-x^{N+1}}{1-x}$.
```

## Infinite Geometric Series

The formula above is exact for any $x \neq 1$. 

**Case 1:** If $|x| > 1$, the series will diverge as $N$ approaches infinity.

**Case 2:** For $|x| < 1$, we have $\lim_{N\to\infty} x^N = 0$. Thus we arrive at:

$$\lim_{N\to\infty}\sum_{n=1}^{N}x^{n} = \frac{x}{1-x} \quad \text{for } |x| < 1$$

And starting from $n=0$:

$$\lim_{N\to\infty}\sum_{n=0}^{N}x^{n} = \frac{1}{1-x} \quad \text{for } |x|<1,  x\neq0$$

```{warning}
The value for $x=0$ depends on our definition of $0^0$, which depending on the context may be either undefined or defined as 1.
```

## Related Infinite Sums

We can derive several related infinite sums from the basic formula:

### Alternating Series

$$\sum_{n=1}^{\infty}(-1)^n x^{n} = \sum_{n=1}^{\infty}(-x)^{n} = \frac{-x}{1-(-x)} = \frac{-x}{1+x}$$

### Even Powers

$$\sum_{n=1}^{\infty}x^{2n} = \sum_{n=1}^{\infty}(x^2)^{n} = \frac{x^2}{1-x^2}$$

## Applications in Time Series

Geometric series appear in several important contexts:

1. **MA($\infty$) Representation:** Moving average models can be expressed as infinite sums involving geometric series.

2. **Autocorrelation Functions:** The ACF of AR processes involves geometric decay.

3. **Spectral Density:** Derivations of spectral densities for ARMA processes rely on geometric series manipulations.

4. **Impulse Response:** The response of a linear filter to an impulse can be expressed using geometric series.

## Example: AR(1) Model

Consider a simple AR(1) process:

$$x_t = \phi x_{t-1} + w_t$$

where $|\phi| < 1$ and $w_t$ is white noise.

We can recursively substitute to express $x_t$ in terms of the infinite past:

\begin{align}
x_t &= \phi x_{t-1} + w_t\\
&= \phi(\phi x_{t-2} + w_{t-1}) + w_t\\
&= \phi^2 x_{t-2} + \phi w_{t-1} + w_t\\
&= \phi^3 x_{t-3} + \phi^2 w_{t-2} + \phi w_{t-1} + w_t\\
&\vdots\\
&= \sum_{j=0}^{\infty} \phi^j w_{t-j}
\end{align}

This infinite sum converges because $|\phi| < 1$, demonstrating how geometric series ensure the **stationarity** of AR processes.

---

**Next:** Introduction to ARMA Models

## Further Reading

- Shumway & Stoffer, Section 1.3: Time Series Models
- Properties of infinite series in complex analysis
- Applications to difference equations
