# 5.4 STL Decomposition

Having explained [classical approaches to time series decomposition](02_classical_decomposition.md) and [local regression](03_loess.md), we are now in a position to understand how *Seasonal-Trend decomposition using LOESS*[^1] (STL). @Cleveland_1990 introduced STL to compensate for the [shortcomings of classical decomposition](02_classical_decomposition.md#drawbacks-to-classical-decomposition).

[^1]: @Cleveland_1990 write that the "L" stands for LOESS. For the purposes this book we will only need the [narrower univariate LOWESS](03_loess.md#lowess-algorithm) algorithm rather than the multivariate LOESS.

## Multiple Time Scales

As presented, STL can only decompose a single seasonality. [](https://doi.org/10.48550/arXiv.2107.13462) generalize STL to handle multiple seasonal lengths (e.g. both a daily and yearly seasonality in temperature) in *Multiple Seasonal-Trend decomposition using LOESS* (MSTL). MSTL is implemented in the newer releases of `statsmodels` as `statsmodels.tsa.seasonal.MSTL`. While MSTL can be a useful tool, in practice it is not currently widely adopted. In general, I would recommend use of frequency domain techniques for analyzing multiple seasonal lengths in a single time series. While not as immediately interpretable as STL or MSTL, frequency domain methods more than make up for the increased complexity with their tremendous ability to tease out the contributions of different seasonal trends.