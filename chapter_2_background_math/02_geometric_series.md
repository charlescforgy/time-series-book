# Geometric Series

Geometric series appear frequently in time series analysis, particularly when working with ARMA models and spectral analysis. Understanding their properties is essential for many derivations in this course.

## Finite Geometric Series

Let $S = x + x^{2} + x^{3} + \ldots + x^{N} = \sum_{n=1}^{N}x^{n}$. This is known as a **geometric progression**.

Now consider $xS$:

$$xS = x^{2} + x^{3} + x^{4} + \ldots + x^{N+1} = \sum_{n=2}^{N+1}x^{n}$$

Subtracting these:

$$
\begin{equation}
\begin{split}
(1-x)S &= S - xS\\
&= (x + x^{2} + x^{3} + \ldots + x^{N}) - (x^{2} + x^{3} + x^{4} + \ldots + x^{N+1})\\
&= x - x^{N+1}\\
&= x(1-x^{N})
\end{split}
\end{equation}
$$

Therefore:

$$
S = x\frac{1-x^{N}}{1-x} \quad \text{for } x \neq 1
$$ (geometric-from-1)

```{note}
When we instead sum from $n=0$, the formula becomes $\frac{1-x^{N+1}}{1-x}$.
```

## Infinite Geometric Series

Eq. {eq}`geometric-from-1` is exact for any $x \neq 1$. 

**Case 1:** If $|x| > 1$, the series will diverge as $N$ approaches infinity.

**Case 2:** For $|x| < 1$, we have $\lim_{N\to\infty} x^N = 0$. Thus we arrive at:

$$
\begin{equation}
\lim_{N\to\infty}\sum_{n=1}^{N}x^{n} = \frac{x}{1-x} \quad \text{for } |x| < 1
\end{equation}
$$ (geometric-infinite-1)

And starting from $n=0$:

$$
\begin{equation}
\lim_{N\to\infty}\sum_{n=0}^{N}x^{n} = \frac{1}{1-x} \quad \text{for } |x|<1,  x\neq0
\end{equation}
$$ (geometric-infinite-0)

```{Note}
The value of Eq. {eq}`geometric-infinite-0` for $x=0$ depends on our definition of $0^0$, which depending on the context may be either undefined or defined as 1.
```

## Related Infinite Sums

We can derive several related infinite sums from the basic formula:

### Alternating Series

$$\sum_{n=1}^{\infty}(-1)^n x^{n} = \sum_{n=1}^{\infty}(-x)^{n} = \frac{-x}{1-(-x)} = \frac{-x}{1+x}$$

### Even Powers

$$\sum_{n=1}^{\infty}x^{2n} = \sum_{n=1}^{\infty}(x^2)^{n} = \frac{x^2}{1-x^2}$$

We will come back to Eq.s {eq}`geometric-infinite-0` and {eq}`geometric-infinite-1` throughout the course of this book.