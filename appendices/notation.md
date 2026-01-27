# Table of Notations

The following table gives a brief overview of the notations used in this book along with their definitions and examples of how they might appear in the text. 

| Symbol | Meaning | Examples | 
| --- | --- | --- |
| Lower case unbolded letter | Scalar or realization/observation of random variable | $x$, $\alpha$ |
| Upper case unbolded letter | Random variable | $X$, $Y$  |
| Lower case bolded letter | Vector | $\mathbf{x}$, $\boldsymbol{\alpha}$ |
| Upper case bolded letter | Matrix  | $\boldsymbol{\Phi}$, $\mathbf{R}$ |
| $\stackrel{\triangle}{=}$| Defined as | $\boldsymbol{\theta}\stackrel{\triangle}{=}(\theta_0, \theta_1,\ldots,\theta_{p-1})$ |
| $\hat{}\,$ over lower case letter | Statistical estimator | $\hat{\mu}$, $\hat{\sigma}^2$|
| $\hat{}\,$ over upper case letter | Operator | $\hat{\mathbb{O}}$|
| $\mathbb{E}[\cdot]$ | Expectation | $\mathbb{E}[x]$, $\mathbb{E}[(x_t-\mu_x)(y_t-\mu_y)]$|
| $\mathbb{P}(\cdot)$ | Probability | $\mathbb{P}(x)$, $\mathbb{P}(x_i\|\theta)$ |
|$P(\cdot)$| Probability function (discrete) or probability density function (continuous) | $\sum_{x_i} P(x_i)$ |
| $\mathbb{B}$ | Backshift operator | $\mathbb{B}x_t=x_{t-1}$|
| $\mathbb{B}^{-1}$ | Forward-shift operator | $\mathbb{B}^{-1}x_{t-1}=x_t$|
| Variable with subscript | Value of *discrete* variable at given index or series indexed by given variable | $x_{t-2}$, $\theta_q$ |
| Variable followed by $(\cdot)$ | Value of *continuous* variable at given value or function of given variable | $x(t-2)$ |
| Variable with subscript and superscript | Value of discrete variable at lower index *conditioned on* (i.e. given) known values up to upper index | $x_{t}^{t-1}$|
| $\mathbb{V}[\cdot]$ or $\sigma_{\cdot}^2$ | Variance | $\mathbb{V}[x_t]$, $\sigma_x^2$ |
| $\text{Cov}(\cdot,\cdot)$ or $\sigma_{\cdot,\cdot}$ | Covariance | $\text{Cov}(\mathbf{X}, \mathbf{Y})$, $\sigma_{x,y}$|
| $\text{Cor}(\cdot,\cdot)$ | Correlation | $\text{Cor}(\mathbf{X}, \mathbf{Y})$|
| $\gamma(h)$ or $\gamma_x(h)$ | Autocovariance of series $x$ at lag $h$ | $\gamma(2)$, $\gamma_x(0)$|
| $\rho(h)$ or $\rho_x(h)$ | Autocorrelation of series $x$ at lag $h$ | $\rho(2)$, $\rho_x(0)$|
| $\gamma_{x,y}(h)$ | Cross-covariance of series $x$ and $y$ at lag $h$ | $\gamma_{x,y}(2)$, $\gamma_{x,y}(0)$|
| $\rho_{x,y}(h)$ | Cross-correlation of series $x$ and $y$ at lag $h$ | $\rho_{x,y}(2)$, $\rho_{x,y}(0)$|
| $\|\|\cdot\|\|$ | Vector norm | $\|\|\mathbf{u}\|\|$ |
|$\sim$ | Distributed as | $x_t\sim \chi_1^2$|
|$wn(\cdot,\cdot)$ | White noise process or distribution |$w_t\sim wn(0,\sigma_w^2)$|
|$\mathcal{N}(\cdot,\cdot)$ | Gaussian process or distribution |$w_t\sim \mathcal{N}(0,\sigma_w^2)$|
| $\Re(\cdot)$ | Real portion of complex number | $\Re(z)$ |
| $\Im(\cdot)$ | Imaginary portion of complex number | $\Im(z)$ | 