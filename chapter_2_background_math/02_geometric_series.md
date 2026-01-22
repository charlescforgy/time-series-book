# 2.2 Geometric Series

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
$$ (series-from-1)

Therefore:

$$
\begin{equation}
S = x\frac{1-x^{N}}{1-x} \quad \text{for } x \neq 1.
\end{equation}
$$ (geometric-from-1)

```{note} Summing from $0$
When we instead sum from $n=0$, the formula becomes $\frac{1-x^{N+1}}{1-x}$.
```

::::{tip} Problem
Prove that  $\sum_{n=0}^{N}x^{n}=\frac{1-x^{N+1}}{1-x} \quad \text{for } x \neq 0,1$.

:::{dropdown} Click to reveal solution
**Solution:** This follow immediately from the derivation in Eq. {eq}`series-from-1`. Let $T = 1 + x + x^2 + \ldots + x^{N} = \sum_{n=0}^{N}x^{n}$.

$$
\begin{equation}
\begin{split}
(1-x)T &= T - xT\\
&= (1+ x + x^{2} + \ldots + x^{N}) - (x + x^{2} + x^{3} + \ldots + x^{N+1})\\
&= 1 - x^{N+1}\\
\end{split}
\end{equation}
$$

Thus we arrive at
$$
T = \frac{1 - x^{N+1}}{1-x}
$$
:::
::::


## Infinite Geometric Series

Eq. {eq}`geometric-from-1` is exact for any $x \neq 1$. We can divide its applications into two cases:

**Case 1:** If $|x| > 1$, the series will diverge as $N$ approaches infinity.

**Case 2:** For $|x| < 1$, we have $\lim_{N\to\infty}x(1- x^N) = x$. Thus we arrive at:

$$
\begin{equation}
\lim_{N\to\infty}\sum_{n=1}^{N}x^{n} = \frac{x}{1-x} \quad \text{for } |x| < 1
\end{equation}
$$ (geometric-infinite-1)

And starting from $n=0$, $\lim_{N\to\infty}(1- x^N) = 1$, yielding:

$$
\begin{equation}
\lim_{N\to\infty}\sum_{n=0}^{N}x^{n} = \frac{1}{1-x} \quad \text{for } |x|<1,  x\neq0
\end{equation}
$$ (geometric-infinite-0)

The value of Eq. {eq}`geometric-infinite-0` for $x=0$ depends on our definition of $0^0$, which in different contexts may be either undefined or defined as 1.

## Related Infinite Sums

We can derive several related infinite sums from the basic formula:

### Alternating Series

$$\sum_{n=1}^{\infty}(-1)^n x^{n} = \sum_{n=1}^{\infty}(-x)^{n} = \frac{-x}{1-(-x)} = \frac{-x}{1+x}$$

### Even Powers

$$\sum_{n=1}^{\infty}x^{2n} = \sum_{n=1}^{\infty}(x^2)^{n} = \frac{x^2}{1-x^2}$$

We will come back to Eq.s {eq}`geometric-infinite-1` and {eq}`geometric-infinite-0` throughout the course of this book.

::::{tip} Problem
Find the values of  $\sum_{n=0}^{\infty}\frac{1}{4^n}$ and $\sum_{n=1}^{\infty}\frac{1}{4^n}$.

:::{dropdown} Click to reveal solution
**Solution:**

$$
\begin{split}
\sum_{n=0}^{\infty}\frac{1}{4^n} &=  \frac{1}{1-\frac{1}{4}}\\
&= \frac{1}{\frac{3}{4}}\\
&= \frac{4}{3}
\end{split}
$$

$$
\begin{split}
\sum_{n=1}^{\infty}\frac{1}{4^n} &=  \frac{\frac{1}{4}}{1-\frac{1}{4}}\\
&= \frac{\frac{1}{4}}{\frac{3}{4}}\\
&= \frac{1}{3}
\end{split}
$$

Note that summing from 1 instead of 0 reduces the sum by exactly 1 as it removes the term $x^0=1$.
:::
::::
