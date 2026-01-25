# 2.5 Information Criteria

## Model Evaluation

In data science, we are often presented with the necessity to compare models to find the best for our particular use case. Quite often, this boils down to a tradeoff between accuracy and interpretability. Different scenarios call for striking a different balance. Competing on a leaderboard may call for a purely *predictive* "black box" model that prioritizes accuracy above all other metrics. In contrast, much of the day-to-day work of a practicing data scientist may align more closely with a *prescriptive* framework that prioritizes usable business intelligence stemming from interpretable models.

:::{note} Predictive vs. Prescriptive Data Science
As a data scientist, you should always be asking yourself where your client's needs fall on the continuum of predictive vs. prescriptive data science. Real life data science projects more frequently fail due to a poor understanding of the client's needs than due to a lack of technical skills. 
:::

### Cross-Validation and Train/Test Splits

As a data scientist, you are probably well acquainted with the use of cross-validation and train/test splits. Recall that standard cross-validation and resampling techniques [cannot be used in time series analysis](../chapter_1_introduction/02_characteristics.md#temporal-ordering). While methods such as the block bootstrap can still be used, they are less powerful than the standard bootstrap.

Use of a train/test split can be less valuable in time series analysis for the simple reason that we are often presented with very small datasets. It is not at all unusual to be presented with $104$ observations covering two years of weekly data, or even $60$ observations covering five years of monthly data[^1]. In such cases, holding out $20\%$ of the data for a test split substantially degrades the predictive power of an already small dataset while still only providing a small sample upon which to test accuracy.

[^1]: Actually, there's a good chance you'll have even fewer as at least a couple of observations are likely to be missing. In such scenarios, it is usually necessary to come up with a realistic interpolation scheme first.

For these reasons and others, we would like to find a way to supplement or even replace cross-validation and train/test splits by creating a metric to objectively evaluate models trained on an entire dataset. The methods we will employ consist of *information criteria*.

## Information Criteria

### Accuracy vs. Complexity

Information criteria are built around the premise that we wish to find ways to balance rewarding models for greater accuracy with penalizing them for greater complexity. Put differently, information criteria ask the question "Does the additional complexity added to a model by a new parameter justify its inclusion by a **sufficient** increase in accuracy?"

In essence information criteria consist of two terms:

1. A penalty for greater complexity as measured by parameter count.
2. A reward for greater accuracy as measured by likelihood.

:::{note} Likelihood

*Likelihood* can be thought of as a cousin of Bayesian posterior probability. Namely, the likelihood function $\mathcal{L}$ for $n$ observations with $p$ parameter(s) $\boldsymbol{\theta}\stackrel{\triangle}{=}(\theta_0, \theta_1,\ldots,\theta_{p-1})$ is defined as

$$
\mathcal{L}_n(\boldsymbol{\theta})= \prod_{i=1}^n \mathbb{P}(x_i| x_1,x_2,\ldots,x_{i-1},\boldsymbol{\theta})
$$

i.e. the product of probabilities that we would observe $x_i$ given parameter(s) $\boldsymbol{\theta}$ and all prior observations through $x_{i-1}$. Note that likelihood **is not a probability density function** itself and has no requirement to sum to unity.
:::

In general we use use the log-likelihood denoted as $\ell_n(\boldsymbol{\theta})$ and defined as

$$
\begin{equation}
\ell_n(\boldsymbol{\theta}) \stackrel{\triangle}{=} \ln{\big(\mathcal{L}_n(\boldsymbol{\theta})\big)}.
\end{equation}
$$ (log-likelihood-def)

Since $\mathcal{L}$ is a strictly positive function, the same parameters that maximize $\mathcal{L}$ will also maximize $\ell$.

::::{tip} Problem
Why are we not concerned that taking the logarithm of $\mathcal{L}$ will result in a logarithm of zero error or complex numbers stemming from the logarithm of negative numbers?

:::{dropdown} Click to reveal solution
**Solution:** $\mathbb{P}(x_i|\theta)$ is a probability density and as such can never be negative. While in theory one could construct a scenario in which $\mathbb{P}(x_i|\theta)$ is $0$ (e.g. the probability of observing a $7$ on a single die roll), in practice this will only arise if our model is mis-specified and/or there's an error in our data. A correctly specified model should never "argue with the data" by predicting zero probability density at the point of an actual observation.
:::
::::

### Akaike Information Criterion

The *Akaike information criterion* (AIC), formulated by Hirotugu Akaike and introduced in the early 1970s ([](https://doi.org/10.1007/978-1-4612-1694-0_15)) is designed to approximate the Kullback-Leibler (KL) divergence between a suggested model and the true (unknown) process model. The AIC of a model $S$ with $p$ parameters can be defined as

$$
\begin{equation}
-2\ell_{S}+2p
\end{equation}
$$ (aic-def)

:::{warning} AIC Definition

There are multiple competing definitions of AIC in use, the definition in Eq. {eq}`aic-def` follows the usage in `statsmodels`, in which case *lower* AIC indicates better model performance. Other sources such as [](https://doi.org/10.1007/978-0-387-21736-9) differ by a factor of $2$ or **even** $\mathbf{-2}$. While dividing by positive $2$ will not change the ordering of models, dividing by $-2$ will reverse the order resulting in *higher* AIC being better.

Always check whether your program considers higher or lower AIC to be better, and never compare AIC values across programs without first verifying that they use the same definition!
:::

Note that as written $\ell$, and consequently AIC, will depend on the number of observations in our sample. This is usually not an issue as we generally compare models trained on the same dataset. Nevertheless, you should keep this factor in mind anytime you are tempted to compare AIC across different projects with different datasets.

### Bayesian Information Criterion

The *Bayesian information criterion* is, on the surface, extremely similar to AIC. BIC is defined as

$$
\begin{equation}
-2\ell_{S}+p\ln(n)
\end{equation}
$$ (bic-def)

for model $S$ with $p$ parameters and $n$ observations in the training data.

:::{warning} BIC Definition
As with AIC above, our definition in Eq. {eq}`bic-def` follows the definition used in `statsmodels` in which lower BIC corresponds to better model performance. Other sources differ in their definitions by a factor of $\pm 2$.
:::

At first glance, it might seem strange that BIC penalizes the number of observations; isn't having a larger training dataset a good thing?  The rationale is that we should be more certain about the likelihood if we have more observations with which to calculate it, thus the same $\ell$ at different $n$ values should be treated differently.

### Significance of AIC or BIC Values

Note that when comparing models using AIC or BIC, the raw numbers have almost no significance. Instead, you should focus on the *relative* values of how far models fall from each other. I.e., $20$ vs. $10$ should be treated the same as $20,010$ vs. $20,000$, as in both cases $\Delta=10$. So how large a $\Delta$ do we require to indicate significance? [](https://doi.org/10.1177/0049124104268644) gives a nice guideline for AIC that I would also consider valid for BIC:

- $|\Delta|\leq2$: Very little evidence to prefer either model.
- $4\leq|\Delta|\leq7$: Some evidence to prefer better scoring model.
- $|\Delta|>10$: Very strong evidence to prefer better scoring model.

### AIC vs. BIC

While similar in form to AIC, and often used in the same role, it is worth noting that from a *theoretical* standpoint, AIC and BIC are in fact very different. As mentioned, AIC was derived from an information-theoretic standpoint with the goal of approximating the KL divergence between a proposed model and the true generating model. Crucially, AIC does not assume that the true model is in our set of models–it merely asks how similar a given model is to the true model.

While BIC can also be motivated from an information-theoretic standpoint, this is not its standard interpretation. Instead, BIC is motivated from a Bayesian perspective[^2]. BIC starts with the assumption that the true model is lurking somewhere in our set of possible models and seeks to discover which model is most probable to be the true one. Assuming a uniform prior distribution over models, it can be shown that the best BIC exactly maximizes the posterior probability of a model. Even without a uniform prior, BIC is generally understood to select the model with the highest posterior probability.

[^2]: Strictly, AIC could also be motivated by a Bayesian argument by starting with a different prior.

Differences in philosophy notwithstanding, in practice AIC and BIC tend to give very similar results. The main difference is that BIC weights the number of parameters more heavily than AIC ($2$ for AIC vs. $\ln(n)$ for BIC). As a result, BIC often tends to favor slightly more parsimonious but less accurate models than AIC. Put differently, AIC is more susceptible to picking overfit models, while BIC is more susceptible to picking underfit models. Some studies indicate that AIC outperforms BIC for small sample sizes, while BIC excels for larger sample sizes. Studies have also demonstrated that BIC may perform better for models with small or large effect sizes, while AIC may perform better for moderate effect sizes ([](https://doi.org/10.1037\%2Fa0027127)).

Ultimately, my advice is to use both AIC and BIC. Chances are they'll agree anyway in more than half the cases you're likely to encounter. When they disagree, ask why they are likely to be disagreeing. Is AIC picking an overfit model, or is BIC picking an underfit one? How large are the effect sizes? Finally, what's most valuable for your client, parsimony and interpretability or predictive accuracy? There is no definitive best in the AIC vs. BIC debate, but these questions can help you identify the best one for a given scenario.

### Other Information Criteria

AIC and BIC are by far the most widely used information criteria. There are a handful of other ones in use that are worth being aware of that we will touch on briefly.

1. **Akaike information criteria, corrected (AICc):** As noted [above](#aic-vs-bic), AIC can pick overfit models, especially for small sample sizes. AICc addresses this by introducing an additional penalty term to more harshly penalize the number of parameters for models trained on small datasets.

$$
\begin{equation}
\begin{split}
AICc &\stackrel{\triangle}{=} AIC + \frac{2p^2+2p}{n-p-1}\\
&= -2\ell_S + \frac{2np}{n-p-1}
\end{split}
\end{equation}
$$

2. **Hannan-Quinn information criteria (HQIC):** Frequently cited but rarely used in practice, HQIC in effect strikes a balance between AIC and BIC by adding an addition level of logarithm and a factor of $2$ to the second term in BIC.

$$
\begin{equation}
HQIC \stackrel{\triangle}{=} -2\ell_{S}+2\,p\ln(\ln(n))
\end{equation}
$$

3. **Mallow's $\mathbf{C_p}$:** Very similar to AIC but limited to use in linear regression. Mallow's $C_p$ balances lack of fit measured by the residual sum of squares (RSS) with model complexity measured by parameter count and estimated population variance.

$$
\begin{equation}
C_p \stackrel{\triangle}{=} \frac{1}{n}\big(RSS +2\,p\,\hat{\sigma}^2\big)
\end{equation}
$$

::::{tip} Problem
Can information criteria such as AIC be used for random forests? If so, how would you go about it? If not, why not?

:::{dropdown} Click to reveal solution
**Solution:** No, information criteria cannot be used for random forests (at least not without a great many assumptions). Information criteria use the number of parameters of a model and the likelihood, both of which are problematic for random forests:

1. There is no clear way to count the number of parameters in a random forest. Do we use the number of trees, the depth of the trees, a combination of the two, or something else entirely?
2. Even if we define a way to count the number of parameters in a random forest, there is no direct way to calculate the likelihood. Linear models can be calculated using maximum likelihood estimation (MLE). Indeed, the standard solution to ordinary least squares is equivalent to MLE. In contrast, random forests are not trained with MLE, and do not have any likelihood function defined.
:::
::::