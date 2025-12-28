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

## Expectation

The most important operator in statistics and data science is the *expectation* operator $\mathbb{E}[F(x)]$, usually first encountered in the context of the arithmetic mean.

```{note}
Following standard statistical notation, we will not use a hat for the expectation operator. We will use the notations $\mathbb{E}$ to distinguish the expectation operator from a function.
```

Expectation is defined as:

$$
\begin{equation}
	\mathbb{E}[F(x)] \stackrel{\triangle}= \begin{cases}
	    \sum_{x} F(x)P(x) & \text{discrete}\ x \\
		\int F(x)P(x) dx  &  \text{continuous}\ x  \\
	\end{cases}
\end{equation}
$$ (expectation-def)

where $P(x)$ is the probability distribution of the random variable $x$.

In general, we will not explicitly reference both the discrete and continuous cases in this book. Instead, we will use the notation $\mathbb{E}$ or even just one of the two methods in Eq. {eq}`expectation-def` with an understanding that a reference to either one implicitly refers to both unless specified otherwise.

### Linearity of Expectation

An important property of expectation is that it is a linear operator. We can demonstrate this fact by proving that $\mathit{\mathbb{E}[aF(x) + bG(y)]} = \mathit{a\mathbb{E}[F(x)] + b\mathbb{E}[G(y)]}$ as follows:

$$
\begin{split}
	\mathit{\mathbb{E}[aF(x) + bG(y)]} &= \int \int (aF(x) + bG(y)) P(x, y)\, dx\, dy \\
	&= \int \int aF(x) P(x, y)\, dx\, dy + \int \int bG(y) P(x, y)\, dx\, dy \\
	&= a\int \int F(x) P(x, y)\, dx\, dy + b\int \int G(y) P(x, y)\, dx\, dy \\
	&= a \int F(x) P(x)\, dx + b\int G(y) P(y)\, dy \\
	&= a\mathbb{E}[F(x)] + b\mathbb{E}[G(y)]
\end{split}
$$ (linearity-expectation)

where in the fourth line we have used the definition of marginal probabilities: $P(x) = \int P(x, y)\, dy$ and $P(y) = \int P(x, y)\, dx$.

```{note}
In most scenarios both $F$ and $G$ will be functions of the same variable. We have presented them as functions of different variables for the sake of proving linearity for the more general case of different variables.
```

```{dropdown} Click to reveal solution
**Problem:** Why can we simplify $\int \int F(x) P(x, y)\, dx\, dy$ to $\int F(x) P(x)\, dx$? What property of marginal probabilities justifies this step?

**Solution:** Since $F(x)$ doesn't depend on $y$, we can factor it out of the inner integral:
$$\int \int F(x) P(x, y)\, dx\, dy = \int F(x) \left(\int P(x, y)\, dy\right) dx$$

The inner integral $\int P(x, y)\, dy$ marginalizes over all possible values of $y$, giving us the marginal probability $P(x)$. This is because the joint probability $P(x, y)$ must integrate to $1$ over all values of $y$ for each fixed $x$. Therefore:
$$\int F(x) \left(\int P(x, y)\, dy\right) dx = \int F(x) P(x)\, dx$$
```

### Moments

## Variance

## Covariance
