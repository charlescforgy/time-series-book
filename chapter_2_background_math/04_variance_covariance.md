# Properties of Variance and Covariance

We will make heavy use of both variance and covariance (in particular the *autovariance* and *autocovariance*) throughout the book. This chapter presents a refresher on these topics building up to the forms used in time series analysis.

## Operators

There are several *operators* we will encounter in this book. Values such as mean, variance, and covariance can all be cast in the operator formalism. In later chapters we will introduce the backshift and Fourier operators. So what *is* and operator?

An operator, often expressed with a "hat" as $\hat{\mathbb{O}}$, is defined as a rule that maps a member of a set to another member. Thus, an operator could just be a function such as $\hat{5}$, defined as multiplying by $5$ (i.e. $f(x)=5x$). However, the most common operators we will use map one function to another function. Two of the most common such operators are differentiation $\frac{d}{dx}$ or integration $\int dx$.
		
        
*Linear operators* are of particular interest. An operator is a linear operator if it fulfills the following two conditions:
 1. $\hat{\mathbb{O}} a F(x) = a\hat{\mathbb{O}} F(x)$ for any constant $a$.
 2. $\hat{\mathbb{O}} (F(x) + G(y)) = \hat{\mathbb{O}}F(x) + \hat{\mathbb{O}}G(y)$

We may write the two conditions more succinctly as 

$$
\begin{equation}
\hat{\mathbb{O}} (aF(x) + bG(y)) = a\hat{\mathbb{O}}F(x) + b\hat{\mathbb{O}}G(y)
\end{equation}
$$ (linear-operator-conditions)

for any constants $a$ and $b$.

Going forward, we will assume that all operators discussed are linear unless otherwise noted.

**Problem:** Demonstrate that square root is *not* a linear operator.

```{dropdown} Click to reveal solution
**Solution:** Square root violates both of the conditions listed above, i.e. in general $\sqrt{aF(x)} \neq a\sqrt{F(x)}$ and $\sqrt{F(x)+ G(y)} \neq \sqrt{F(x)}+ \sqrt{G(y)}$.
```

**Problem:** Demonstrate that differentiation is a linear operator.

```{dropdown} Click to reveal solution
**Solution:** We can demonstrate this by showing that Eq. {eq}`linear-operator-conditions` holds. If $x$ and $y$ are different variables, then $\frac{d}{dx}bG(y)=0$ and linearity is trivial. If both $F$ and $G$ are functions of $x$, we have: 

$$
\begin{equation}
\begin{split}
\frac{d}{dx}(aF(x) + bG(x)) &= \frac{d}{dx} aF(x) + \frac{d}{dx} bG(x)\\
&= a\frac{d}{dx}F(x) + b\frac{d}{dx}G(x)
\end{split}
\end{equation}
$$

```

### Expectation

### Moments

## Variance

## Covariance
