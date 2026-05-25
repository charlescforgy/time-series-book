# 6.1 Introduction

We are now in a position to use our understanding of time series to create theoretical models to help us both to understand processes from a theoretical point of view (time series analysis) and to predict future values (forecasting). There are two models that lie at the heart of traditional time series analysis:

1. Autoregressive (AR): AR models, which are built on prior observations, can be thought of as an extension of random walks in which the current value depends on one or more prior timesteps. AR models take the form of

$$
x_t = \phi_1 x_{t-1} + \phi_2 x_{t-2}+\ldots+\phi_p x_{t-p}+w_t.
$$

2. Moving average (MA): MA models are built on prior *noise* and generalize the idea of moving averages to any kernel. Importantly, MA models allow negative values and do not require coefficients to sum to unity. MA models take the form of

$$
x_t = w_t + \theta_1 w_{t-1} + \theta_2 w_{t-2}+\ldots+\theta_q w_{t-q}.
$$

After examining MA and AR models in isolation, we will move on to combining them into autoregressive moving average (ARMA) models. The chapter concludes with a discussion of modifications to ARMA models to deal with non-stationary time series, seasonal effects, and exogenous variables.