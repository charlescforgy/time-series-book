# 6.4 ARMA Models (Under Construction)


## Autocovariance and Autocorrelation of AR($p$) Models

### $\psi$ Weight Representation

[](https://doi.org/10.1007/978-1-4419-0320-4) chapter 3.3 provides three broad methods to calculate the theoretical autocovariance and autocorrelation of AR models (and ARMA models in general). We will focus on the first here.

The first method relies on representing the model in the form of Eq. {eq}`psi-weight-def`. $\gamma(h)$ is then calculated in the same fashion as Eq. {eq}`ar1-acf-derivation`

$$
\begin{equation}
\begin{split}
\gamma(h) &= \text{Cov}(x_{t+h}, x_t)\\
&=\mathbb{E}[x_{t+h}x_t]\\
&=\mathbb{E}\Big[\Big(\sum_{j=0}^{\infty}\psi_j w_{t+h-j}\Big)\Big(\sum_{k=0}^{\infty} \psi_k w_{t-k}\Big)\Big]\\
&= \sigma_w^2 \sum_{i=0}^{\infty} \psi_i\,\psi_{i+h}
\end{split}
\end{equation}
$$ (psi-ar-gamma-0)

where we have used the fact that the noise is iid resulting in zero covariance for different noise terms. Unfortunately, AR($p$) models with $p\geq2$ do not have the same convenient [geometric series representation seen with an AR(1) model](#ar1-autocovariance). Nevertheless, Eq. {eq}`psi-ar-gamma-0` provides an elegant method for calculating the autocovariance, and hence the autocorrelation, of any stationary AR process—or, as we shall see, any stationary ARMA process. The fact that the $\psi$ weights decay exponentially results in it being possible to truncate Eq. {eq}`psi-ar-gamma-0` after say, thirty terms, with relatively little loss in accuracy.

### Recursion Relations

The second and third methods in [](https://doi.org/10.1007/978-1-4419-0320-4) rely on using recursion relations relating higher $h$ values to lower ones. We will not go into great depth regarding this method, but it is instructive to see how it might be applied to an AR(2) model. Let us begin with the AR(2) process $x_t = \phi_1 x_{t-1} + \phi_2 x_{t-2} + w_t$. We can multiply through by $x_{t-h}$ and take the expectation:

$$
\begin{equation}
\begin{split}
    x_t x_{t-h} &=  \phi_1 x_{t-1}x_{t-h} + \phi_2 x_{t-2}x_{t-h} + w_t x_{t-h}\\
    \mathbb{E}[x_t x_{t-h}] &=  \mathbb{E}[\phi_1 x_{t-1}x_{t-h}] + \mathbb{E}[\phi_2 x_{t-2}x_{t-h}] + \mathbb{E}[w_t x_{t-h}]\\
    \mathbb{E}[x_t x_{t-h}] &=  \phi_1\mathbb{E}[ x_{t-1}x_{t-h}] + \phi_2\mathbb{E}[x_{t-2}x_{t-h}] + \mathbb{E}[w_t x_{t-h}]\\
    \gamma(h) &= \phi_1\gamma(h-1) +\phi_2 \gamma(h-2) +  \mathbb{E}[w_t x_{t-h}].
\end{split}
\end{equation}
$$ (recursive-with-noise)

Provided that our AR(2) process is causal (stationary), we can rewrite the final term in Eq. {eq}`recursive-with-noise` as

$$
\begin{equation}
    \mathbb{E}[w_t x_{t-h}] = \mathbb{E}\Big[w_t\sum_{j=0}^{\infty}\psi_j w_{t-h-j}\Big]=0.
\end{equation}
$$ (ma-infty-zero)

Combining Eqs. {eq}`recursive-with-noise` and {eq}`ma-infty-zero` we arrive at the desired recursion relation

$$
\begin{equation}
    \gamma(h) = \phi_1\gamma(h-1) +\phi_2 \gamma(h-2)
\end{equation}
$$ (ar2-acovf-recursion)

Without additional information, we cannot further simplify in Eq. {eq}`ar2-acovf-recursion`. However, dividing through by $\gamma(0)$ generates a recursion relation for the autocorrelation

$$
\begin{equation}
    \rho(h) = \phi_1\rho(h-1) + \phi_2 \rho(h-2).
\end{equation}
$$

We know that $\rho(0)=1$, and we can evaluate $\rho(1)$ using Eq. {eq}`ar2-acovf-recursion`:

$$
\begin{equation}
    \begin{split}
        \gamma(1) &= \phi_1 \gamma(0) + \phi_2 \gamma(-1)\\
        &= \phi_1 \gamma(0) + \phi_2 \gamma(1)\\
        (1-\phi_2)\gamma(1) &= \phi_1 \gamma(0)\\
        \gamma(1) &= \frac{\phi_1}{1-\phi_2}\gamma(0)\\
        \rho(1) &= \frac{\phi_1}{1-\phi_2}\\
    \end{split}
\end{equation}
$$

Thus we can derive $\rho(2)$ as

$$
\begin{equation}
    \rho(2) = \frac{\phi_1^2}{1-\phi_2} + \phi_2,
\end{equation}
$$

and continue extending the recursion relation to higher values of $h$.