# Stationarity

One of the most central concepts in time series analysis is *stationarity*.  Loosely, a stationary time series is one that does not demonstrate any trend or seasonal variation. More concretely, there are two distinct definitions of stationarity:
1. *Strict stationarity* is defined as a time series for which the probability distribution of any subset of observations is identical, i.e. 
$$
P_{x}(x_{t_1}, x_{t_2},...,x_{t_k}) = P_{x}(x_{t_1+h}, x_{t_2+h},...,x_{t_k+h}).
$$

2. *Weak stationarity* is defined as a time series for which the following hold:

	i. The mean $E[x_t]$ is finite and constant across all times $t$.
    
    ii. The variance $\mathbb{V}(x_t)=\gamma(t, t)$ is finite and constant across all times $t$.
    
    iii. The autocovariance is a function of lag alone and not of absolute time, $\gamma(s, t)= \gamma(s+v, t+v)=\gamma(|s-t|)$.

An example of a strictly stationary series would be pure white noise.
