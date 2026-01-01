# Properties of Variance and Covariance

We will make heavy use of both variance and covariance (in particular the *autovariance* and *autocovariance*) throughout the book. This chapter presents a refresher on these topics laying the foundation for the forms used in time series analysis discussed in the next chapter.

## Operators

There are several *operators* we will encounter in this book. Values such as mean, variance, and covariance can all be cast in the operator formalism. In later chapters we will introduce the backshift and Fourier operators. So what *is* an operator?

An operator, often expressed with a "hat" as $\hat{\mathbb{O}}$, is defined as a rule that maps a member of a set to another member. Thus, an operator could just be a function such as $\hat{5}$, defined as multiplying by $5$ (i.e. $f(x)=5\,x$). However, the most common operators we will use map one function to another function. Two such operators are differentiation $\frac{d}{dx}$ and integration $\int dx$.
		
        
*Linear operators* are of particular interest. An operator is a linear operator if it fulfills the following two conditions:
 1. $\hat{\mathbb{O}}\, a F(x) = a\hat{\mathbb{O}}\, F(x)$ for any constant $a$
 2. $\hat{\mathbb{O}}\, (F(x) + G(y)) = \hat{\mathbb{O}}\,F(x) + \hat{\mathbb{O}}\,G(y)$

We may write the two conditions more succinctly as 

$$
\begin{equation}
\hat{\mathbb{O}}\, (aF(x) + bG(y)) = a\,\hat{\mathbb{O}}\,F(x) + b\,\hat{\mathbb{O}}\,G(y)
\end{equation}
$$ (linear-operator-conditions)

for any constants $a$ and $b$.

Going forward, we will assume that all operators discussed are linear unless otherwise noted.

**Problem:** Demonstrate that square root is *not* a linear operator.

```{dropdown} Click to reveal solution
**Solution:** Square root violates both of the conditions listed above, i.e. in general $\sqrt{a\,F(x)} \neq a\,\sqrt{F(x)}$ and $\sqrt{F(x)+ G(y)} \neq \sqrt{F(x)}+ \sqrt{G(y)}$.
```

**Problem:** Demonstrate that differentiation is a linear operator.

```{dropdown} Click to reveal solution
**Solution:** We can demonstrate this by showing that Eq. {eq}`linear-operator-conditions` holds. If $x$ and $y$ are different variables, then $\frac{d}{dx}b\,G(y)=0$ and linearity is trivial. If both $F$ and $G$ are functions of $x$, we have: 

$$
\begin{equation}
\begin{split}
\frac{d}{dx}(a\,F(x) + b\,G(x)) &= \frac{d}{dx} a\,F(x) + \frac{d}{dx} b\,G(x)\\
&= a\,\frac{d}{dx}F(x) + b\,\frac{d}{dx}G(x)
\end{split}
\end{equation}
$$

```

## Expectation

The most important operator in statistics and data science is the *expectation* operator $\mathbb{E}[F(x)]$, usually first encountered in the context of the arithmetic mean.

```{note}
Following standard statistical notation, we will not use a hat for the expectation operator. We will use the notation $\mathbb{E}$ to distinguish the expectation operator from a function.
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

In general, we will not explicitly reference both the discrete and continuous cases in this book. Instead, we will use the notation $\mathbb{E}$ or one of the two methods in Eq. {eq}`expectation-def` with an understanding that a reference to either one implicitly refers to both unless specified otherwise.

### Linearity of Expectation

An important property of expectation is that it is a linear operator. We can demonstrate this fact by proving that $\mathit{\mathbb{E}[a\,F(x) + b\,G(y)]} = \mathit{a\,\mathbb{E}[F(x)] + b\,\mathbb{E}[G(y)]}$ as follows:

$$
\begin{split}
	\mathit{\mathbb{E}[a\,F(x) + b\,G(y)]} &= \int \int (a\,F(x) + b\,G(y))\, P(x, y)\, dx\, dy \\
	&= \int \int a\,F(x)\, P(x, y)\, dx\, dy + \int \int b\,G(y)\, P(x, y)\, dx\, dy \\
	&= a\int \int F(x)\, P(x, y)\, dx\, dy + b\int \int G(y)\, P(x, y)\, dx\, dy \\
	&= a \int F(x)\, P(x)\, dx + b\int G(y)\, P(y)\, dy \\
	&= a\,\mathbb{E}[F(x)] + b\,\mathbb{E}[G(y)]
\end{split}
$$ (linearity-expectation)

where in the fourth line we have used the definition of marginal probabilities: $P(x) = \int P(x, y)\, dy$ and $P(y) = \int P(x, y)\, dx$.

```{note}
In most scenarios both $F$ and $G$ will be functions of the same variable. We have presented them as functions of different variables for the sake of proving linearity for the more general case of different variables.
```
**Problem:** Why can we simplify $\int \int F(x)\, P(x, y)\, dx\, dy$ to $\int F(x)\, P(x)\, dx$? What property of marginal probabilities justifies this step?

```{dropdown} Click to reveal solution
**Solution:** Since $F(x)$ doesn't depend on $y$, we can factor it out of the inner integral:
$$\int \int F(x)\, P(x, y)\, dx\, dy = \int F(x) \left(\int P(x, y)\, dy\right) dx$$

The inner integral $\int P(x, y)\, dy$ is the definition of the marginal probability $P(x)$ in that it sums the joint probability over all possible values of $y$ (which must sum to $1$) to obtain the probability of $x$ alone. Therefore:
$$\int F(x) \left(\int P(x, y)\, dy\right) dx = \int F(x)\, P(x)\, dx$$
```

### Moments

We have not yet specified the identity of $F(x)$. By far, the most commonly used functions are powers of $x$, i.e.

$$
\begin{equation}
    F(x) = x^n, \qquad n=0,1,2,\ldots
\end{equation}
$$ (moment-x-def)

The expectation value of Eq. {eq}`moment-x-def` is the $n\text{th}$ *moment* of $P(x)$:

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

An important theorem states that if the $k\text{th}$ moment $\mathbb{E}[x^{k}]$ is finite, then all moments $j<k$ must also be finite. As a corollary, if $\mathbb{E}[x^{k}]$ is infinite, all moments $m>k$ must also be infinite.

Proof: Let $\mathbb{E}[x^{k}]$ be finite and $j<k$ ($\forall$ read as "for all")

$$
\begin{equation*}
	\begin{split}
		\mathbb{E}[x^{j}] &= \int_{-\infty}^{\infty} x^{j} P(x)\, dx \\
		&= \int_{-\infty}^{-1} x^{j} P(x)\,dx + \int_{-1}^{1} x^{j} P(x)\, dx + \int_{1}^{\infty} x^{j} P(x)\, dx\\
		&\text{note that } |x^{j}| \leq |x^{k}| \, \forall\, \ |x| \geq 1\ \text{and } \ |x^{j}| \leq 1 \, \forall\, |x| \leq 1 \\
		&\leq \Big|\int_{-\infty}^{-1} x^{k} P(x) \,dx\Big| + \int_{-1}^{1} 1 \, P(x)\, dx + \int_{1}^{\infty} x^{k} P(x) \,dx\\
		&\leq \Big|\int_{-\infty}^{-1} x^{k} P(x)\, dx\Big| + 1 + \int_{1}^{\infty} x^{k} P(x)\, dx\\
        &< \infty \\
		%&\text{Q.E.D.}
		\end{split}
	\end{equation*}
$$

where we have used the fact that $\int_{-1}^{1} 1 P(x)\, dx \leq 1$, with equality only occurring if the entire probability mass is contained in the interval $[-1,1]$.

**Problem:** Consider the standard Cauchy distribution (also known as the Cauchy-Lorentz or Lorentz distribution) defined as

$$
P(x) = \frac{1}{\pi}\cdot\frac{1}{1+x^2}
$$

<ol type="a">
  <li>Prove that this is a valid probability distribution.</li>
  <li>What is the first moment of the Cauchy distribution?</li>
  <li>What does part b tell you about higher moments?</li>
</ol>

```{dropdown} Click to reveal solution
**Solution:**

  a. In order to be a valid probability distribution, the Cauchy distribution must satisfy the three Kolmogorov axioms:
  
  1. Non-negativity, i.e. $P(x) \geq 0\,\forall\, x$: This is satisfied because $\frac{1}{1+x^2}$ is positive for all real $x$.
  2. Disjoint summation, i.e. $P(A \,\cup \, B) =P(A) + P(B)$ for $A\,\cap\,B=0$: For two disjoint intervals $A=[a_1,a_2]$ and $B=[b_1,b_2]$, we have

  $$
  P(A \,\cup \, B) = \int_{a_1}^{a_2}P(x) \,dx+\int_{b_1}^{b_2}P(x) \,dx = P(A) + P(B).
  $$

  In texts on mathematical statistics you will find that the above point actually has additional nuance stemming from the definition of Lebesgue integrals and other measure-theoretic arguments. That said, the proof used here is fully sufficient for our purposes.

  3. Summation, i.e. $\int_{-\infty}^{\infty}P(x)\,dx=1$.

  $$
  \begin{equation}
  \begin{split}
    \frac{1}{\pi} \int_{-\infty}^{\infty}\frac{1}{1+x^2}\,dx &= \frac{1}{\pi} \arctan{(x)}\Biggr\rvert_{x=-\infty}^{\infty}\\
    &= \frac{1}{\pi}\Big(\lim_{x\to\infty}\arctan{(x)} -\lim_{x\to-\infty}\arctan{(x)}\Big)\\
    &= \frac{1}{\pi}\big(\frac{\pi}{2}- (-\frac{\pi}{2})\big)\\
    &= \frac{1}{\pi}{\pi}=1.
  \end{split}
  \end{equation}
  $$

  b. We must evaluate the integral $\mathbb{E}[x]$ for the Cauchy distribution:

  $$
    \begin{split}
        \mathbb{E}[x] &= \frac{1}{\pi}\int_{-\infty}^{\infty} \frac{x}{1+x^2}\,dx
    \end{split}
  $$

  Letting $u=1+x^2$ so that $du=2x\,dx$, we have

$$
    \begin{split}
\frac{1}{\pi}\int \frac{x}{1+x^2}\,dx &= \frac{1}{2\pi}\int \frac{du}{u}\\
    &= \frac{1}{2\pi}\ln{(u)}.
    \end{split}
$$

Substituting the definition of $u$ and limits of integration

$$
    \begin{split}
    \frac{1}{2\pi}\ln{(1+x^2)}\Biggr\rvert_{x=-\infty}^{\infty} &= \frac{1}{2\pi}\Big(\lim_{x\to\infty}\ln{(1+x^2)} -\lim_{x\to-\infty}\ln{(1+x^2)}\Big)\\
    &= \frac{1}{2\pi}(\infty-\infty)\\
    &= \infty.
    \end{split}
$$

  c. The fact that the first moment (the arithmetic mean) is not finite guarantees that all higher moments will also not be finite. Thus, the distribution cannot have a defined variance, skew, etc. We will see later in the book that this causes real world problems as many extreme financial events roughly obey a Cauchy distribution.
```

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

One of the most fundamental aspects of time series analysis is understanding the variance of the sum of random variables. Let us begin with the variance of a sum of two random variables, $X$ and $Y$.

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

The last line of Eq. {eq}`var-2-variables` can be rewritten as

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
$$ (var-2-sum)

The above suggests (though does not prove) that the variance of a sum of variables is the sum of all covariance combinations. For random variables $X_{1}, X_{2}, X_{3},\ldots,X_{n}$:

$$
\begin{equation}
	\begin{split}
		\mathbb{V}\biggl(\sum_{i=1}^{n} X_{i}\biggr) &= \sum_{i, j=1}^{n} \text{Cov}(X_{i}, X_{j})\\
		&= \sum_{i=1}^{n} \mathbb{V}(X_{i}) + 2\sum_{i=1,\, j>i} \text{Cov}(X_{i}, X_{j})
	\end{split}
\end{equation}
$$ (var-of-sum-multi)

where the last term sums $i$ to $n-1$ and $j$ to $n$. Eq. {eq}`var-of-sum-multi` can be proven in the same manner as Eq. {eq}`var-2-variables`, though the algebra gets rather intricate. We present a more direct proof in the following problem.

**Problem:** Prove Eq. {eq}`var-of-sum-multi` using the definition of variance and covariance. Hint, express $\mathbb{V}(X)$ as $\text{Cov}(X, X)$.



```{dropdown} Click to reveal solution
**Solution:**

$$
\begin{equation}
	\begin{split}
		\mathbb{V}\biggl(\sum_{i=1}^{n} X_{i}\biggr) &= \text{Cov}\biggl( \sum_{i=1}^{n} X_{i}, \sum_{i=j}^{n} X_{j} \biggl)\\
        &= \text{Cov}(X_1,X_1)+\text{Cov}(X_1,X_2)+\text{Cov}(X_2,X_1)+\ldots+ \text{Cov}(X_n,X_n)\\
        &= \sum_{i, j=1}^{n} \text{Cov}(X_{i}, X_{j})\\
        &= \sum_{i=1}^{n} \mathbb{V}(X_{i}) + 2\sum_{i=1,\, j>i} \text{Cov}(X_{i}, X_{j})
	\end{split}
\end{equation}
$$
```

### Variance of Independent Variables

From Eq. {eq}`var-of-sum-multi` we can see that *if all variables have zero covariance* (most commonly due to independence), the variance of a sum of variables is the sum of the variances of each variable

$$
\begin{equation}
	\mathbb{V}\biggl(\sum_{i=1}^{n} X_{i}\biggr) =  \sum_{i=1}^{n} \mathbb{V}(X_{i}) \qquad \text{for zero covariance}.
\end{equation}
$$ (var-ind-sum)

While it is very tempting to simply assume that Eq. {eq}`var-ind-sum` holds, in real life we must justify its use, either from theoretical analysis and/or empirical evidence.

It should also be recalled that knowing that random variables have zero covariance does not inherently prove independence. As a simple counterexample, consider a random variable $X$ with zero mean and third moment and let $Y=X^2$. An example might be $X \sim N(0,1)$ and $Y = X^2 \sim \chi^2_1$. Clearly, $X$ and $Y$ and highly dependent; for example, knowing that $Y>4$ tells us $|X|>2$. Nevertheless, they still have zero covariance:

$$
\begin{equation*}
	\text{Cov}(X, Y) = \mathbb{E}[XY] - \mathbb{E}[X]\mathbb{E}[Y]= \mathbb{E}[XY] - 0 \cdot \mathbb{E}[Y] = \mathbb{E}[X^3]=0
\end{equation*}
$$

## Variance-Covariance Inequality

**Warning:** The math in this section can get rather heavy. Feel free to skip this section if you're having difficultly. While the material below does add to the overall understanding of future material, it is not absolutely necessary.

### Arithmetic Inequality

From Eq. {eq}`var-2-variables` we see that 

$$
\mathbb{V}(X + Y) =  \mathbb{V}(X) + \mathbb{V}(Y) + 2\,\text{Cov}(X, Y).
$$
By substituting in $-Y$, we arrive at 

$$
\mathbb{V}(X-Y) =  \mathbb{V}(X) + \mathbb{V}(Y) - 2\,\text{Cov}(X, Y).$$

As variances are by definition non-negative, by combining the above two equations we arrive at the inequality $2\,|\text{Cov}(X, Y)| \leq \mathbb{V}(X) + \mathbb{V}(Y)$. We can express this in terms of the arithmetic mean of the variances:

$$
\begin{equation}
	|\text{Cov}(X, Y)| \leq \frac{1}{2}(\mathbb{V}(X) + \mathbb{V}(Y))
\end{equation}
$$ (arth-ineq)

While Eq. {eq}`arth-ineq` is true, we will soon see that we can get an even tighter bound on the inequality.

### Cauchy-Schwarz Inequality

In order to better understand the relation between variance and covariance, we must first define the Cauchy-Schwarz inequality, a valuable inequality from linear algebra. In words, it states that the square of the inner product of two vectors must always be less than or equal to the norm of the first vector squared times the second vector's norm squared.

$$
\begin{equation}
	(\mathbf{u} \cdot \mathbf{v})^{2} \leq \|\mathbf{u}\|^{2}\|\mathbf{v}\|^{2}
\end{equation}
$$ (cauchy-schwarz-square)

or, recognizing that vector norms are always non-negative

$$
\begin{equation}
	|\mathbf{u} \cdot \mathbf{v}| \leq \|\mathbf{u}\|\|\mathbf{v}\|
\end{equation}
$$ (cauchy-schwarz-abs)

Eq.s {eq}`cauchy-schwarz-square` and {eq}`cauchy-schwarz-abs` will be equalities if and only if $\mathbf{u}$ and $\mathbf{v}$ can be expressed as scalar multiples of one another (i.e. lie on the same line). 

If $\mathbf{u}$ and/or $\mathbf{v}$ is the zero vector, the inequality is trivially true, let us prove the inequality when neither is:

$$
\begin{equation*}
	\text{Let } \mathbf{w} \stackrel{\triangle}=  \frac{\mathbf{u}}{\|\mathbf{u}\|} \pm \frac{\mathbf{v}}{\|\mathbf{v}\|}
\end{equation*}
$$

$$
\begin{equation*}
	\begin{split}
		\mathbf{w} \cdot \mathbf{w} &=  \Big(\frac{\mathbf{u}}{\|\mathbf{u}\|} \pm \frac{\mathbf{v}}{\|\mathbf{v}\|}\Big)\cdot \Big(\frac{\mathbf{u}}{\|\mathbf{u}\|} \pm \frac{\mathbf{v}}{\|\mathbf{v}\|}\Big) \\
		&= \frac{\mathbf{u} \cdot \mathbf{u}}{\|\mathbf{u}\|^{2}} 
			\pm 2\frac{\mathbf{u} \cdot \mathbf{v}}{\|\mathbf{u}\| \|\mathbf{v}\|} 
			+ \frac{\mathbf{v} \cdot \mathbf{v}}{\|\mathbf{v}\|^{2}}\\
		&= 1 \pm 2\frac{\mathbf{u} \cdot \mathbf{v}}{\|\mathbf{u}\| \|\mathbf{v}\|} + 1\\
		&= 2 \pm 2\frac{\mathbf{u} \cdot \mathbf{v}}{\|\mathbf{u}\| \|\mathbf{v}\|} \\
		&\text{as with any inner product } \mathbf{w} \cdot \mathbf{w} \geq 0\\
		0 &\leq 2 \pm 2\frac{\mathbf{u} \cdot \mathbf{v}}{\|\mathbf{u}\| \|\mathbf{v}\|} \\
		1 &\geq \frac{| \mathbf{u} \cdot \mathbf{v} |}{\|\mathbf{u}\| \|\mathbf{v}\|} \\
		%1 &\geq \mp \frac{\mathbf{u} \cdot \mathbf{v}}{\|\mathbf{u}\| \|\mathbf{v}\|} \\
		%-1 &\leq \frac{\mathbf{u} \cdot \mathbf{v}}{\|\mathbf{u}\| \|\mathbf{v}\|} \leq 1\\
		\|\mathbf{u}\| \|\mathbf{v}\|  &\geq |\mathbf{u} \cdot \mathbf{v}| \\
	\end{split}
\end{equation*}
$$

### Functions as Infinite Dimensional Vectors

 The Cauchy-Schwarz inequality may be extended to integrals by viewing functions as infinite dimensional vectors living in Hilbert space. Let us imagine we have two continuous functions, $f(x)$ and $g(x)$ that are square-integrable on the interval $[a, b]$. Let us create $n$-dimensional vectors by sampling the value of the function at $n$ evenly spaced points along the interval, producing the vectors
 
 $$
 (f(x_{1}), f(x_{2}),\ldots,f(x_{n})=f(b)), 
 $$
 and
 $$
 (g(x_{1}), g(x_{2}),\ldots,g(x_{n})=g(b)).
 $$

By the Cauchy-Schwarz inequality:

$$
\begin{equation}
	\Big(\sum_{i=1}^{n}f(x_{i})g(x_{i})\Big)^{2} \leq \Big(\sum_{i=1}^{n}f(x_{i})^{2}\Big)\Big(\sum_{i=1}^{n}g(x_{i})^{2}\Big)
\end{equation}
$$

As the term $[\frac{b-a}{n}]^{2}$ is a positive constant for any fixed $a$, $b$, and $n$, we may freely multiply both sides by it:

$$
\begin{equation*}
	\begin{split}
		\Big[\frac{b-a}{n}\Big]^{2}\Big(\sum_{i=1}^{n}f(x_{i})g(x_{i})\Big)^{2} &\leq \Big[\frac{b-a}{n}\Big]^{2}\Big(\sum_{i=1}^{n}f(x_{i})^{2}\Big)\Big(\sum_{i=1}^{n}g(x_{i})^{2}\Big)\\
		\Big(\sum_{i=1}^{n}f(x_{i})g(x_{i})\Big[\frac{b-a}{n}\Big]\Big)^{2} &\leq \Big(\sum_{i=1}^{n}f(x_{i})^{2}\Big[\frac{b-a}{n}\Big]\Big)\Big(\sum_{i=1}^{n}g(x_{i})^{2}\Big[\frac{b-a}{n}\Big]\Big).
	\end{split}
\end{equation*}
$$

Defining $\Delta x$ as $\frac{b-a}{n}$:

$$
\begin{equation*}
	\begin{split}
	\Big(\sum_{i=1}^{n}f(x_{i})g(x_{i})\Delta x\Big)^{2} &\leq \Big(\sum_{i=1}^{n}f(x_{i})^{2}\Delta x\Big)\Big(\sum_{i=1}^{n}g(x_{i})^{2}\Delta x\Big).
	\end{split}
\end{equation*}
$$

Finally, by taking the limit $\lim_{n\to \infty}\Delta x$ we arrive at the Cauchy-Schwarz inequality in integral form:

$$
\begin{equation}
	\Big(\int_{a}^{b} f(x)g(x) dx\Big)^{2} \leq \int_{a}^{b} f(x)^{2} dx \int_{a}^{b} g(x)^{2} dx
\end{equation}
$$ (cauchy-schwarz-integral)

### Geometric Inequality

We can use the Cauchy-Schwarz inequality to derive a tighter upper bound on the covariance of two variables than that found in Eq. {eq}`arth-ineq`. We will only explicitly prove the bound for the discrete case of sample covariance, but by Eq. {eq}`cauchy-schwarz-integral` the bound will also hold for the continuous case. Let

$$
\mathbf{X'}\stackrel{\triangle}= \frac{1}{\sqrt{n-1}}(\mathbf{X}-\mu_{x})
$$ 

for random variable $\mathbf{X} = (x_{1}, x_{2},\ldots,x_{n})$, and let $\mathbf{Y'}$ be defined analogously. By the definitions of (sample) variance and covariance, 

$$\mathbb{V}(\mathbf{X}) = \|\mathbf{X'}\|^{2} =\mathbf{X'} \cdot \mathbf{X'},
$$

$$
\mathbb{V}(\mathbf{Y}) = \|\mathbf{Y'}\|^{2}= \mathbf{Y'} \cdot \mathbf{Y'},
$$ 

and

$$
(\text{Cov}(\mathbf{X}, \mathbf{Y}))^{2} = (\mathbf{X'} \cdot \mathbf{Y'})^{2}.
$$

 Substituting these definitions into Eq. {eq}`cauchy-schwarz-square`, we conclude that 

$$
\begin{equation}
    (\mathbf{X'} \cdot \mathbf{Y'})^{2} \leq \|\mathbf{X'}\|^{2}\|\mathbf{Y'}\|^{2},
\end{equation}
$$ 
or

$$
\begin{equation}
	(\text{Cov}(\mathbf{X}, \mathbf{Y})))^{2} \leq \mathbb{V}(\mathbf{X}) \mathbb{V}(\mathbf{Y})
\end{equation}
$$

Equivalently, by taking the square roots of both sides and recognizing that covariance could be negative, we arrive at

$$
\begin{equation}
	|\text{Cov}(\mathbf{X}, \mathbf{Y})| \leq \sqrt{\mathbb{V}(\mathbf{X}) \mathbb{V}(\mathbf{Y})},
\end{equation}
$$

or more concisely

$$
\begin{equation}
		|\sigma_{x, y}| \leq \sigma_{x}\sigma_{y}.
\end{equation}
$$ (sigma-var-cov-ineq)

We thus arrive at the tighter bound that the absolute value of the covariance must always be less than or equal to the *geometric* mean of the variances.

## Correlation

### Drawbacks to Covariance

While generally useful, the covariance does have potential drawbacks. In particular, $\text{Cov}(\mathbf{X}, \mathbf{Y})$ has units of $\mathbf{X} \times \mathbf{Y}$, causing two major difficulties:
1. The exact value obtained will depend on the units used, for example the covariance of height and weight will be different in imperial vs. metric.
2. It can be very difficult to assess if the value of the covariance is practically significant, e.g. is a covariance of $3 $ kg cm high or low? 

### Correlation Definition

To overcome these difficulties with covariance, it is often convenient to instead normalize covariance to a unitless quantity bounded by $[-1, 1]$:

$$
\begin{equation}
	\text{Cor}(\mathbf{X}, \mathbf{Y}) \stackrel{\triangle}= \frac{\sigma_{x, y}}{\sigma_{x}\sigma_{y}}
\end{equation}
$$ (corr-def)

This quantity is referred to as the *correlation*. Note that the Cauchy-Schwarz inequality as applied in Eq.{eq}`sigma-var-cov-ineq` guarantees that Eq. {eq}`corr-def` will always lie in $[-1, 1]$. Of course, even when using correlation determining if a value such as $0.7$ should be considered a high correlation or not will be context and situation dependent.

[^1]: Some texts define moments as $\int (x-c)^n P(x)\, dx$ for some constant $c$. We will assume $c=0$ unless otherwise specified.