# Information Criteria

## Model Evaluation

In data science, we are often presented with the necessity to compare models to find the best for our particular use case. Quite often, this boils down to a tradeoff between accuracy and interpretability. Different scenarios call for striking a different balance. Competing on a leaderboard may call for a purely *predictive* "black box" model that prioritizes accuracy above all other metrics. In contrast, much of the day-to-day work of a practicing data scientist may fall into more of a *prescriptive* framework that prioritizes usable business intelligence stemming from interpretable models.

```{note} Predictive vs. Prescriptive Data Science
As a data scientist, you should always be asking yourself where your client's needs fall on the continuum of predictive vs. prescriptive data science. Real life data science projects more frequently fail due to a poor understanding of the client's needs than due to a lack of technical skills. 
```

### Cross-Validation and Train/Test Splits

As a data scientist, you are probably well acquainted with the use of cross-validation and train/test splits. Recall that standard cross-validation and resampling techniques [cannot be used in time series analysis](../chapter_1_introduction/02_characteristics.md#temporal-ordering). While methods such as the block bootstrap can still be used, they are less powerful than the standard bootstrap.

Use of a train/test split can be less valuable in data science for the simple reason that we are often presented with very small datasets. It is not at all unusual to be presented with $104$ observations covering two years of weekly data, or even $60$ observations covering five years of monthly data[^1]. In such cases, holding out $20\%$ of the data for a test split substantially degrades the predictive power of an already small dataset.

[^1]: Actually, there's a good chance you'll have even fewer as at least a couple of observations are likely to be missing. In such scenarios, it is usually necessary to come up with a realistic interpolation scheme first.

For these reasons and others, we would like to find a way supplement or even replace cross-validation and train/test splits by creating a metric to objectively evaluate models trained on an entire dataset. The methods we will employ consist of *information criteria*.

## Information Criteria

### Accuracy vs. Complexity

Information criteria are built around the premiss that we wish to find ways to balance rewarding models for greater accuracy with penalizing them for greater complexity. Put differently, information criteria ask the question "Does additional complexity added to a model justify its inclusion by a **sufficient** increase in accuracy?"

In essence information criteria consist of two terms:

1. A penalty for greater complexity as measured by parameter count.
2. A reward for greater accuracy as measured by likelihood.

```{note} Likelihood

*Likelihood* can be thought of a cousin of Bayesian posterior probability. Namely, the likelihood function $\mathcal{L}$ for $t$ observations with parameter(s) $\theta$ is defined as

$$
\mathcal{L}_n(\theta)= \prod_{i=1}^t p(x_t| x_1,x_2,\ldots,x_{i-1}\theta)
$$

i.e. the product of probabilities that we would observe $x_i$ given parameter(s) $\theta$ and all prior observations through $x_{i-1}$. Note that likelihood **is not a probability density function** itself and in general there is no requirement for it to sum to unity.
```

In general we use use the log-likelihood denoted as $\ell_n(\theta)$ and defined as 

$$
\begin{equation}
\ell_n(\theta) \stackrel{\triangle}{=} \ln{\big(\mathcal{L}_n(\theta)\big)}.
\end{equation}
$$ (log-likelihood-def)

Since $\mathcal{L}$ is a strictly positive function, the same parameters that maximize $\mathcal{L}$ will also maximize $\ell$.

**Problem:** Why are we not concerned that taking the logarithm of $\mathcal{L}$ will result in a logarithm of zero error or complex numbers stemming from the logarithm of negative numbers?

```{dropdown} Click to reveal solution
**Solution:**

$p(x_t|\theta)$ is a probability density and as such can never be negative. While in theory one could construct a scenario in which $p(x_t|\theta)$ is $0$ (e.g. the probability of observing a $7$ on a single die roll), in practice this will only arise if our model is mis-specified and/or there's an error in our data. A correctly specified model should never "argue with the data" by predicting zero probability density at the point of an actual observation.
```

### Akaike Information Criterion

The *Akaike Information Criterion* (AIC), formulated by Hirotugu Akaike and introduced in the early 1970s is designed to approximate the Kullback-Leibler (KL) divergence between a suggested model and the true (unknown) process model. The AIC of a model $S$ with $p$ parameters can be defined as

$$
\begin{equation}
\ell_{S}-p
\end{equation}
$$ (aic-def)

```{warning} AIC Definition

There are multiple competing definitions of AIC in use, the definition in Eq. {eq}`aic-def` follows [](https://doi.org/10.1007/978-0-387-21736-9), in which case *higher* AIC is better. Other sources differ by a factor of $2$ or **even** $\mathbf{-2}$ (including `statsmodels`). While multiplying by positive $2$ will not change the ordering of models, multiplying by $-2$ will reverse the order resulting in *lower* AIC being better.

Always check whether your program considers higher or lower AIC to be better, and never compare AIC values across programs without first verifying they use the same definition!
```

Note that as written, $\ell$, and consequently AIC, will depend on the number of observations in our sample. This is usually not an issue as we generally compare models trained on the same dataset. Nevertheless, you should bare this consideration in mind anytime you are tempted to compare AIC across your different projects.