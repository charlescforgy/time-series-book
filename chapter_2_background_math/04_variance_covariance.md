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
		\int F(x)P(x)\, dx  &  \text{continuous}\ x  \\
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
**Problem:** Why can we simplify $\int \int F(x) P(x, y)\, dx\, dy$ to $\int F(x) P(x)\, dx$? What property of marginal probabilities justifies this step?

```{dropdown} Click to reveal solution
**Solution:** Since $F(x)$ doesn't depend on $y$, we can factor it out of the inner integral:
$$\int \int F(x) P(x, y)\, dx\, dy = \int F(x) \left(\int P(x, y)\, dy\right) dx$$

The inner integral $\int P(x, y)\, dy$ is the definition of the marginal probability $P(x)$ in that it sums the joint probability over all possible values of $y$ (which must sum to $1$) to obtain the probability of $x$ alone. Therefore:
$$\int F(x) \left(\int P(x, y)\, dy\right) dx = \int F(x) P(x)\, dx$$
```

### Moments

So far, we have not yet specified the identity of "$F(x)$." By far, the most commonly used functions are powers of $x$, i.e.

$$
\begin{equation}
    F(x) = x^n, \qquad n=0,1,2,\ldots
\end{equation}
$$ (moment-x-def)

The expectation value of Eq. {eq}`moment-x-def` is the $n$th *moment* of $P(x)$:

$$
\begin{equation}
    \mathbb{E}[x^n] = \int x^n P(x)\, dx,
\end{equation}
$$ (moment-def)
where we are usually interested in cases of $n=1-4$[^1]:

1. The first moment is the arithmetic mean denoted as $\mu_x$.
2. The second moment relates to the variance, or how widely spread the distribution is.
3. The third moment relates to the skew, or how symmetric the function is.
4. The fourth moment relates to the kurtosis, or the "fatness" of the tails.

**Problem:** Why do we not consider the case of $n=0$?
 
```{dropdown} Click to reveal solution
**Solution:** The zeroth moment is simply $\int P(x)\,dx$, which must equal $1$ for any correctly normalized probability distribution. The only scenario in which we'd need to calculate the zeroth moment is when we do not know the correct normalization constant for our probability distribution.
```

### Finite Moments

An important theorem states that if the $k^{th}$ moment $\mathbb{E}[x^{k}]$ is finite, then all moments $j<k$ must also be finite. As a corollary, if $\mathbb{E}[x^{k}]$ is infinite, all moments $m>k$ must also be infinite.

Proof: Let $\mathbb{E}[x^{k}]$ be finite and $j<k$ ($\forall$ read as "for all")

$$
\begin{equation*}
	\begin{split}
		\mathbb{E}[x^{j}] &= \int_{-\infty}^{\infty} x^{j} P(x)\, dx \\
		&= \int_{-\infty}^{-1} x^{j} P(x)\,dx + \int_{-1}^{1} x^{j} P(x)\, dx + \int_{1}^{\infty} x^{j} P(x)\, dx\\
		&\text{note that } |x^{j}| \leq |x^{k}|\  \forall \ |x| \geq 1\ \text{and } \ |x^{j}| \leq 1 \ \forall\ |x| \leq 1 \\
		&\leq \Big|\int_{-\infty}^{-1} x^{k} P(x) \,dx\Big| + \int_{-1}^{1} 1 P(x)\, dx + \int_{1}^{\infty} x^{k} P(x) \,dx\\
		&\leq \Big|\int_{-\infty}^{-1} x^{k} P(x)\, dx\Big| + 1 + \int_{1}^{\infty} x^{k} P(x)\, dx\\
        &< \infty \\
		%&\text{Q.E.D.}
		\end{split}
	\end{equation*}
$$

where we have used the fact that $\int_{-1}^{1} 1 P(x)\, dx \leq 1$, with equality only occurring if the entire probability mass is contained in the interval $(-1,1)$.

## Variance

The variance of a random variable $X$ is defined as 

$$
\begin{equation}
\mathbb{V}(X)\stackrel{\triangle}= \mathbb{E}[(X-\mu_{x})^{2}], 
\end{equation}
$$ (var-def)

and is often denoted as $\sigma_x^2$. The variance gives us a measure of how widely the distribution is spread about the mean. In practice, we more commonly make use of the *standard deviation* $\sigma_x$, which is simply the square root of the variance.

As written, the variance is slightly different than our definition of the second moment.

```{note}
The quantity $\mathbb{E}[(X-\mu_x)^n]$ is referred to as the *central moment*.
```

By exploiting the linearity of expectation we can express Eq. {eq}`var-def` using the first and second moments exclusively:

$$
\begin{equation}
	\begin{split}
	\mathbb{E}[(X-\mu_{x})^{2}] &=\mathbb{E}[X^{2}-2X\mu_{x}+\mu_{x}^{2}] \\
	&=\mathbb{E}[X^{2}]- \mathbb{E}[2X\mu_{x}] + \mathbb{E}[\mu_{x}^{2}] \\
	&=\mathbb{E}[X^{2}]- 2\mu_{x}\mathbb{E}[X] + \mu_{x}^{2}\mathbb{E}[1] \\
	&=\mathbb{E}[X^{2}]- 2\mu_{x}^{2} + \mu_{x}^{2}\\
	&=\mathbb{E}[X^{2}]- \mu_{x}^{2}\\
	&=\mathbb{E}[X^{2}]- (\mathbb{E}[X])^{2}\\
					%&\text{Q.E.D.}
	\end{split}
\end{equation}
$$ (var-as-first-second-moments)

## Covariance

The covariance of two random variables $X, Y$ is defined as

$$
\begin{equation}
	\text{Cov}(X, Y) \stackrel{\triangle}= \mathbb{E}[(X-\mu_{x})(Y-\mu_{y})].
\end{equation}
$$

Covariance is also written as $\sigma_{x, y}$. Note that $\text{Cov}(X, X) = \mathbb{V}(X)$. 

Unlike variance, which is never negative, covariance can be negative, zero, or positive.Following the same logic used in Eq. {eq}`var-as-first-second-moments`, we can also express the covariance as

$$
\begin{equation}
    \text{Cov}(X, Y) = \mathbb{E}[XY] - \mathbb{E}[X]\mathbb{E}[Y]
\end{equation}
$$

**Problem:** Why can variance never be negative?

```{dropdown} Click to reveal solution
**Solution:** Variance is defined as $\mathbb{E}[(X-\mu_x)^2]=\int (X-\mu_x)^2\,P(x)\,dx$. By definition, $P(x)$ can never be negative for any $x$, while $(X-\mu_x)^2$ is the square of a real number and also cannot be negative. Thus, all terms in the integral are non-negative.
```

Covariance gives us a measure of how much random variables change in tandem with one another.

## Variance of Sums of Random Variables

One of the most fundamental aspects of time series analysis is understanding the variance of a sum of random variables. Let us begin with the variance of a sum of two random variables, $X$ and $Y$.

### Variance of Multiple Random Variables

$$
\begin{equation}
	\begin{split}
	    \mathbb{V}(X+Y) &= \mathbb{E}[(X+Y-\mu_{x}-\mu_{y})^{2}]\\
		&= \mathbb{E}[X^{2}] + \mathbb{E}[Y^{2}] + 2\mathbb{E}[XY] - 2\mathbb{E}[X\mu_{x}]\\
		&- 2\mathbb{E}[X\mu_{y}] - 2\mathbb{E}[Y\mu_{x}]- 2\mathbb{E}[Y\mu_{y}] + 2\mathbb{E}[\mu_{x}\mu_{y}]\\
		&+\mathbb{E}[\mu_{x}^{2}] +\mathbb{E}[\mu_{y}^{2}] \\
		&= \mathbb{E}[X^{2}] + \mathbb{E}[Y^{2}] + 2\mathbb{E}[XY] - \mu_{x}^{2} -\mu_{y}^{2} - 2\mu_{x}\mu_{y}\\
		&= (\mathbb{E}[X^{2}] -\mu_{x}^{2}) + (\mathbb{E}[Y^{2}] -\mu_{y}^{2}) + 2(\mathbb{E}[XY]-\mu_{x}\mu_{y})\\
		&= \mathbb{V}(X) + \mathbb{V}(Y) + 2\,\text{Cov}(X, Y)
    \end{split}
\end{equation}
$$ (var-2-variables)

The last line of Eq. {eq}`var_2_variables` can be rewritten as

$$
\begin{equation}
	\text{Cov}(X, X) + \text{Cov}(Y,Y) + \text{Cov}(X,Y) + \text{Cov}(Y, X),
\end{equation}
$$

or, renaming the variables as $X_{1}$ and $X_{2}$

$$
\begin{equation}
	\sum_{i, j=1}^{2} \text{Cov}(X_{i}, X_{j}).
\end{equation}
$$

The above suggests (though does not prove) that the variance of a sum of variables is the sum of all covariance combinations. For random variables $X_{1}$, $X_{2}$, $X_{3}$,...,$X_{n}$:

$$
\begin{equation}
	\begin{split}
		\mathbb{V}\biggl(\sum_{i=1}^{n} X_{i}\biggr) &= \sum_{i, j=1}^{n} \text{Cov}(X_{i}, X_{j})\\
		&= \sum_{i=1}^{n} \mathbb{V}(X_{i}) + 2\sum_{i=1, j>i}^{n} \text{Cov}(X_{i}, X_{j})
	\end{split}
\end{equation}
$$ (var-of-sum-multi)

Eq. {eq}`var-of-sum-multi` can be proven in the same manner as Eq. {eq}`var-2-variables`, though the algebra gets quite complicated.

### Variance of Independent Variables

From Eq. {eq}`var-of-sum-multi` we can see that *if all variables have zero covariance* (most commonly due to independence), the variance of a sum of variables is the sum of the variances of each variable

$$
\begin{equation}
	\mathbb{V}\biggl(\sum_{i=1}^{n} X_{i}\biggr) =  \sum_{i=1}^{n} \mathbb{V}(X_{i}) \qquad \text{for zero covariance}.
\end{equation}
$$ (var-ind-sum)

While it is very tempting to simply assume that Eq. {eq}`var_ind_sum` holds, in real life we must justify its use, either from theoretical analysis and/or empirical evidence.

It should also be recalled that knowing that random variables have zero covariance does not inherently prove independence. As a simple counterexample, consider a random variable $X$ with zero mean and third moment and let $Y=X^2$. An example might be $X \sim N(0,1)$ and $Y = X^2 \sim \chi^2_1$. Clearly, $X$ and $Y$ and highly dependent; for example, knowing that $Y>4$ tells us $|X|>2$. Nevertheless, they still have zero covariance:

$$
\begin{equation*}
	\text{Cov}(X, Y) = \mathbb{E}[XY] - \mathbb{E}[X]\mathbb{E}[Y]= \mathbb{E}[XY] - 0 \cdot \mathbb{E}[Y] = \mathbb{E}[X^3]=0
\end{equation*}
$$

## Variance-Covariance Inequality

### Arithmetic Inequality

[^1]: Some texts define moments as $\int (x-c)^n P(x)\, dx$ for some constant $c$. We will assume $c=0$ unless otherwise specified.

[