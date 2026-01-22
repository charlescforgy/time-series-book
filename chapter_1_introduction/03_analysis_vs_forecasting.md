# 1.3 Time Series Analysis vs. Forecasting

One aspect that time series share with broader data science is that we are interested both in understanding historical patterns and forecasting or predicting future values[^1]. Let's take a look at some of the methods and goals of each.

[^1]: Some texts define prediction and forecasting as being subtly different. In particular, "Prediction" is sometimes used to mean the general task of predicting an unknown target variable from known features, while "forecasting" is reserved for time series analysis in which we are forecasting future values of the same series we are observing. This book will not make such distinctions and will use the terms interchangeably.

## Analysis

Time series *analysis* is concerned with the underlying structure of the time series and how values relate to one another. Typical questions asked in analysis include

- Does a higher stock market return today increase the probability of a low return tomorrow?
- What time of day sees the most visitors to our website?
- How does colder weather affect flu transmission?
- What factors are most closely associated with our supply chain failing to deliver goods on time? Are there warning signs that our supply chain is close to being overloaded?
- Is there any correlation between the number of visitors to our park and wildlife migration?

We will explore multiple methods throughout this book focused on such questions. The workhorse of this analysis will be the ARIMA family of models, but other techniques such as STL Decomposition and cross-correlation analysis will also factor in heavily.

## Forecasting

Time series *forecasting* is primarily concerned with predicting future values. Forecasting is often an outgrowth of analysis, for example once we've constructed an ARIMA model of our series we can also use this model to provide predictions. Pure forecasting techniques can also rely on methods such as deep learning or boosted forests, which can be extremely accurate but tend to offer little insight into the underlying behavior of the time series.

Of course, it is not uncommon in data science to be interested in both analysis and forecasting. In other cases, clients may be interested in forecasting but only willing to accept forecasts that have clear motivations and interpretations. In such scenarios, use of a single model such as ARIMA will often serve both as our analysis tool and (interpretable) prediction method.

## Filtering and Smoothing: Predicting the Present

A special set of sub-cases that does not fall neatly into either analysis or forecasting is the problem of *filtering* and *smoothing*. We will delve into the difference between the two terms in future chapters. For now, it is sufficient to think of them as "predicting the present."

You may well be asking why we would ever need to predict the present—aren't we observing it right now? Filtering problems arise whenever we have less than perfect fidelity in our models and sensors, especially when we wish to combine data from multiple noisy sensors in *sensor fusion*. A common example might be tracking an aircraft on radar. Our analysis of its trajectory suggests it should be at one location, while our radar tracking places it elsewhere. To add to the confusion, perhaps the aircraft itself is transmitting a location agreeing with neither. Filtering and smoothing deal with averaging together disparate and noisy observations to obtain a better estimate than any single sensor or model could provide.

For those interested, we provide a sneak peek at a widely-used type of filter known as a *Kalman filter* below.


```{figure} images/kalman_filter.svg
---
width: 90%
name: kalman-filter-fig
---
Kalman filter demonstrating combining prior forecasts with current noisy sensor data.
```

## Distinguishing Analysis from Forecasting

It's relatively easy to build competitions and leaderboards for forecasting. In contrast, it's difficult to objectively rank insight into historical patterns. For this reason, new data scientists often focus nearly exclusively on the forecasting portion of time series, with their interest in analysis confined to its use to create forecasting models.

While entirely understandable, this mindset misses the broader question of "What is the best way to help my client or employer?". There are instances in which your goal as a data scientist is confined to improving forecasts to, say, ensure adequate cloud compute power is available. However, it is at least as common to be tasked with time series analysis as part of a broader project that requires insights into trends or causative factors. In such cases, highly interpretable pure analysis methods will generally provide far more value than exquisite but opaque forecasting methods. As always in data science, a thorough understanding of the business goals and use cases must precede any decisions of model choice.

:::{tip} Problem
Go to a time series model leaderboard or benchmark and look at the data set(s) used. Are they primarily focused on forecasting or analysis? How might the competition or benchmark change if the focus changed?
:::