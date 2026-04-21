# 5.4 STL Decomposition

Having explained [classical approaches to time series decomposition](02_classical_decomposition.md) and [local regression](03_loess.md), we are now in a position to understand how *Seasonal-Trend decomposition using LOESS*[^1] (STL). @Cleveland_1990 from Bell Labs and the University of Michigan introduced STL to compensate for the [shortcomings of classical decomposition](02_classical_decomposition.md#drawbacks-to-classical-decomposition). As we shall see, STL grows naturally out of augmenting classical decomposition with LOESS. Using LOESS gives the flexibility to calculate trends for all data points, unlike moving averages which truncate the first and final values. Furthermore, unlike the [X-11 family](01_intro.md) which only handles quarterly or monthly seasonalities, STL can handle any number of seasons per cycle.

[^1]: @Cleveland_1990 write that the "L" stands for LOESS. We will maintain this terminology, though for the purposes this book we will only need the [narrower univariate LOWESS](03_loess.md#lowess-algorithm) algorithm rather than the multivariate LOESS.

## STL Algorithm

### Log Transfomation

Unlike with classical decomposition, STL only works with additive trend and seasonal components. Data with a multiplicative trend must be [log transformed to use STL](../chapter_4_enforcing_stationarity/05_log_transform.md). In practice, this is rarely a major impediment to using STL, though applying a log transform does cause interpretability to go slightly down.


### STL Loops

The STL algorithm runs a nested loop of a set of functions until the algorithm reaches a set stopping point. We will defer discussion of deseasonalization for the moment to first understand the overall procedure. Letting $n_{i}$ be the number of passes in the inner loop and $n_{o}$ be the number in the outer loop, the algorithm is as follows:
1. Initialize robustness weights for LOESS to $1$ and the trend to $0$.

 	1.1 Detrend the series using the most recently calculated trend (set at $0$ initially).

 	1.2 Run LOESS (weighted by robustness) to smooth the detrended data.

 	1.3 Calculate the seasonal component ([next section](#stl-deseasonalization)) and deseasonalize the data.
 	
    1.4 Apply another round of (weighted) LOESS to determine the trend to be used as the starting point for the next iteration

2. Calculate the residuals. If $n_o>0$, calculate robustness weights to quantify each residual's degree of extremity for the LOESS algorithm (i.e. to what extent it should be considered an outlier) and return to step 1.1, otherwise stop.

The fact that LOESS can output smoothed data with the [same length as the original data](03_loess.md#weighting-points-near-the-edges) is crucial for step 1.2. With a standard moving average, each pass of a length $m$ filter would delete $m$ ($m$ even) or $m-1$ ($m$ odd) observations; running multiple loops on a small dataset would quickly whittle it down to nothing. It is only because we use LOESS that we can free run the inner loop for multiple iterations.

By default, `statsmodels` does not use robust fitting (i.e. $n_0=0$) and uses $5$ inner loops ($n_i=5$). This can be changed by setting `robust=True`, which by default will use $n_i=2$ and $n_o=15$. $n_i$ and $n_o$ can also be set manually in the `fit` method by calling `STL(data).fit(inner_iter=n_i, outer_iter=n_o)`.

### STL Deseasonalization
 	
In step 1.3 we referenced deseasonalization without explaining how this is perfromed. A major advantage of STL over classical decomposition is that it allows seasonality to vary over time instead of locking us into a single seasonality for the entire series. The algorithm used in 1.3 functions as follows:
1. The deseasonalization step starts by creating an intermediate series $C_i$ consisting of the results of step 1.2 temporarily padded at both ends.

2. $L_i$ is calculated from $C_i$ by smoothing $C_i$ with a "low-pass" filter[^2] consisting of two sequential moving averages of length $m$, followed by a moving average of length $3$ (which removes the padding from step 1), and finishing with LOESS.

3. Seasonal components $S_i$ are are calculated as $S_i=C_i-L_i$ (using the non-padded $C_i$), and $S_i$ is subtracted from the series for this iteration's deseasonalized series. Subtracting $L_i$ removes low-frequency long-term components from the seasonality.

[^2]: We will explain why this filter is referred to as "low-pass" in the chapter covering the frequency domain.

An important advantage of STL is that, while we do still need to specify and $m$ value for the seasonal length, we are no longer constrained to have a single average $\hat{S}_t$ as we were in [classical decomposition](02_classical_decomposition.md#additive-method). Instead, the use of LOESS in step 2 allows for variations in the magnitude of the seasonal component (though not its length) over the course of the data.

## Plotting STL Decomposition Components

:::{figure} images/stl-decomp-unemployment.png
---
width: 95%
name: stl-decomp-unemployment
---
US employment rate from 1948 through 2025 from the [Federal Reserve Bank of St. Louis](https://fred.stlouisfed.org/series/UNRATENSA) with STL decomposition using the default settings in `statsmodels` of $n_i=5$ and $n_o=0$. Plot consists of original series (top), trends and cycles (second from top), seasonal contribution (third from top), and residual variation not explained (bottom).
:::

Applying the [assessment method from classical decomposition](02_classical_decomposition.md#assessing-decomposition-quality) results in $F_T=0.953$ and $F_S=0.625$. Compared with the classical decomposition results of $F_T=0.928$ and $F_S=0.379$, this indicates that STL has done a marginally better job isolating the trend but a substantially better job isolating the season component.

## Multiple Time Scales

As presented, STL can only decompose a single seasonality. [](https://doi.org/10.48550/arXiv.2107.13462) generalize STL to handle multiple seasonal lengths (e.g. both a daily and yearly seasonality in temperature) in *Multiple Seasonal-Trend decomposition using LOESS* (MSTL). MSTL is implemented in the newer releases of `statsmodels` as `statsmodels.tsa.seasonal.MSTL`. While MSTL can be a useful tool, in practice it is not currently widely adopted. In general, I would recommend use of frequency domain techniques for analyzing multiple seasonal lengths in a single time series. While not as immediately interpretable as STL or MSTL, frequency domain methods more than make up for the increased complexity with their tremendous ability to tease out the contributions of different seasonal trends.