# Complex Numbers

## Definition of Complex Numbers

### Imaginary Components

Complex numbers expand the real numbers by adding the *imaginary* square root of $-1$

$$
i \stackrel{\triangle}{=}\ \sqrt{-1},
$$
thus $\sqrt{-9}=3i, \sqrt{-2}=\sqrt{2}i$, etc.
```{note}
In some fields, in particular electrical engineering, the square root of -1 is denoted by *j* instead of *i*. This is also the convention used in NumPy. 
```

### Complex: Real plus Imaginary

Speaking more broadly, a *complex* number has both real and imaginary components. A complex number $z$ can be expressed as
$$
z = x+yi
$$
with $x$ being the real components and $y$ the imaginary. Of course, we can treat any purely real or imaginary number as a complex number by setting $y$ or $x$ to zero, respectively.

### Complex Conjugate

A key property of complex number is the *complex conjugate* $z^{*}$ defined as 
$$
z^{*} = (a+bi)^{*} = a-bi
$$
i.e. the complex conjugate leaves the real components unchanged and flips the sign of the imaginary components. From this it immediately follows that any purely real number is its own complex conjugate.

### Magnitude of Complex Numbers

The magnitude of a complex number, denoted as $|z|$, is given as

$$
\begin{equation}
\begin{split}
|z| &= |a+bi|\\
&= \sqrt{(a+bi)(a+bi)^{*}}\\
&= \sqrt{(a+bi)(a-bi)}\\
&= \sqrt{a^2-abi+abi+b^2}\\
&= \sqrt{a^2+b^2}
\end{split}
\end{equation}
$$ (magnitude-def)

where we have used the fact that $-b^2i^2=b^2$.

Eq. {eq}`magnitude-def` suggests that complex numbers may be viewed as vectors in the real plane with length $|z|$. 

```{figure} images/complex2polar.svg
---
width: 80%
name: complex-to-polar
---
Conversion between polar and Cartesian forms of complex numbers.
```

You may recognize {ref}`complex-to-polar` as an example of an *isomorphism* between $\mathbb{C}$ and $\mathbb{R^2}$. If you're not familiar with the term isomorphism, you can consider it a fancy way of saying "mapping."

### Polar Form

{ref}`complex-to-polar` suggests the *polar form* of complex with magnitude $r=|z|$ and angle $\theta=\arctan{(\frac{y}{x})}$, allowing the substitution

$$
\begin{equation}
z = r \cos{(\theta)} + ir\sin{(\theta)}.
\end{equation}
$$ (polar-def)

While Eq. {ref}`polar-def` may be suggestive as a heuristic, we will see in the next section that it actually touches upon a deep concept in mathematics.

## Euler's Formula: The "Jewel" of Mathematics

Euler's formula, called "our jewel" by Richard Feynman, establishes a profound connection between trigonometry and complex numbers.

**Euler's Formula:**

$$
\begin{equation}
e^{i\theta} = \cos(\theta) + i \sin(\theta)
\end{equation}
$$

Or, multiplying through by $r$ for magnitudes different than 1:

$$
\begin{equation}
r e^{i\theta} = r \cos(\theta) + i r \sin(\theta)
\end{equation}
$$



### Euler's Identity

When $\theta=\pi$, we have:

$$e^{i\pi} = -1 + i \cdot 0$$

or

$$e^{i\pi} + 1 = 0$$

This is known as **Euler's identity**, which relates five fundamental mathematical constants: $e$, $i$, $\pi$, 1, and 0.

### Proof of Euler's Formula

Let $f(\theta) = \frac{\cos(\theta) + i \sin(\theta)}{e^{i\theta}}$

$$
\begin{equation}
\begin{split}
\frac{d f(\theta)}{d\theta} &= \frac{d}{d\theta} \frac{\cos(\theta) + i \sin(\theta)}{e^{i\theta}}\\
&= \frac{d}{d\theta} e^{-i \theta}(\cos(\theta) + i \sin(\theta))\\
&= -i e^{-i \theta}(\cos(\theta) + i \sin(\theta)) + e^{-i \theta}(-\sin(\theta) + i \cos(\theta))\\
&= e^{-i \theta}(-i\cos(\theta) + \sin(\theta) - \sin(\theta) + i \cos(\theta))\\
&= e^{-i \theta}(0)\\
&= 0
\end{split}
\end{equation}
$$

Since $\frac{df}{d\theta} = 0$, $f(\theta)$ is constant, and we need only establish its value at a single point.

At $\theta = 0$:

$$
f(0) = \frac{1 + 0i}{1} = 1
$$

Therefore $f(\theta) = 1$ for all $\theta$, or:

$$
\begin{equation}
e^{i\theta} = \cos(\theta) + i \sin(\theta)
\end{equation}
$$

```{note}
This formula can also be proven by replacing the exponential and trigonometric functions with their Taylor series expansions and grouping the real and complex terms.
```
## Applications of Euler's Formula

### Solving Problems Involving $i$

Euler's formula can help solve otherwise extremely difficult problems involving $i$. To see this, note that given that $e^{i\theta} = \cos(\theta) + i \sin(\theta)$, at $\theta=\frac{\pi}{2}$ we have 

$$
\begin{equation}
\begin{split}
e^{\frac{i\pi}{2}} &= \cos(\frac{\pi}{2}) + i \sin(\frac{\pi}{2})\\
&=0+1i\\
&=i
\end{split}
\end{equation}
$$ (i-as-exponent)

### Solving $i^i$

Eq. {eq}`i-as-exponent` allows us to solve problems that at first may seem intractable. For example, let us calculate $i^i$:

$$
\begin{equation}
\begin{split}
i^i &= (e^{\frac{i\pi}{2}})^i\\
&= e^{\frac{i^2\pi}{2}}\\
&= e^{-\frac{\pi}{2}}.
\end{split}
\end{equation}
$$

Somewhat surprisingly, it turns out that $i^i$ is a real number slightly greater than $\frac{1}{5}$.

### Sine and Cosine Addition Formulae

Use of Euler's formula can simplify trigonometric derivations by transforming geometric problems into algebraic ones. A pair of trigonometric identities that we will make use of later in the book are the *sine and cosine addition formulae*, given as

$$
\begin{equation}
\sin{(\alpha + \beta)} = \sin{(\alpha)}\cos{(\beta)} + \cos{(\alpha)}\sin{(\beta)}
\end{equation}
$$ (sin-add-formula)

and

$$
\begin{equation}
\cos{(\alpha + \beta)} = \cos{(\alpha)}\cos{(\beta)} - \sin{(\alpha)}\sin{(\beta)}.
\end{equation}
$$

While these can be proven purely geometrically, Euler's formula gives us an arguably far simpler algebraic method to prove them instead. 
Using $\Re(z)$ to denote the real component of $z$ and $\Im(z)$ to denote the imaginary component, we have

$$
\begin{equation}
\cos{(\alpha + \beta)} = \Re(e^{i(\alpha + \beta)})
\end{equation}
$$ (cos-as-real)

and 

$$
\begin{equation}
\sin{(\alpha + \beta)} = \Im(e^{i(\alpha + \beta)}).
\end{equation}
$$ (sin-as-imaginary)

Note that

$$
\begin{equation}
\begin{split}
e^{i(\alpha + \beta)} &= e^{i\alpha} e^{i\beta} \\
&= (\cos{(\alpha)} + i\sin{(\alpha)})(\cos{(\beta)} + i\sin{(\beta)})\\
&= \cos{(\alpha)}\cos{(\beta)} - \sin{(\alpha)}\sin{(\beta)} \\ 
&\quad + i\sin{(\alpha)}\cos{(\beta)} + i\cos{(\alpha)}\sin{(\beta)}\\
&= [\cos{(\alpha)}\cos{(\beta)} - \sin{(\alpha)}\sin{(\beta)}]\\ 
&\quad + i[\sin{(\alpha)}\cos{(\beta)} + \cos{(\alpha)}\sin{(\beta)}].
\end{split}
\end{equation}
$$ (simplify-exp-alpha-beta)


Combining Eq. {eq}`sin-as-imaginary` with Eq. {eq}`simplify-exp-alpha-beta` we arrive at the sine addition formula

$$
\sin{(\alpha + \beta)} = \sin{(\alpha)}\cos{(\beta)} + \cos{(\alpha)}\sin{(\beta)}.
$$

Similarly, combining Eq. {eq}`cos-as-real` with Eq. {eq}`simplify-exp-alpha-beta` gives us the cosine addition formula

$$
\cos{(\alpha + \beta)} = \cos{(\alpha)}\cos{(\beta)} - \sin{(\alpha)}\sin{(\beta)}.
$$

## Problems


**Problem:** We have seen that generating the square root of $-1$ requires expanding beyond the real numbers. Does calculating $\sqrt{i}$ require expansion beyond the complex numbers, or is contained in the complex numbers?

**Solution:** We can solve this using Eq. {eq}`(i-as-exponent)`:
$$
\begin{equation}
\begin{split}
\sqrt{i} &= \sqrt{e^{\frac{i\pi}{2}}}\\
&= (e^{\frac{i\pi}{2}})^(\frac{1}{2})\\
&= e^{\frac{i\pi}{4}}\\
&= \cos{(\frac{\pi}{4})} + i \sin{(\frac{\pi}{4})}\\
&= \frac{1}{\sqrt{2}}\big(1+i\big).
\end{split}
\end{equation}
$$
In more advanced math texts this is explained as stemming from the *algebraic closure* of the complex numbers. Put simply, any meaningful formula involving complex numbers has a solution that is also a complex number.