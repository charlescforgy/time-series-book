# **1.1** What is a Time Series?

## Data Type

By the strict mathematical definition, a time series is defined as a series of observations ordered by time. Note that we have not defined the observations as *numeric* values, a time series can be any sort of observation, including not only numeric data such as stock prices or infection rates, but also observations such as:
- Images indexed by time (i.e. videos)
- System health (healthy, warning, etc.) every hour
- User sentiment on social media every day
- The geographic location of IP addresses visiting our website every second

While the above are all examples of time series, this text will focus on purely numeric data. Non-numeric data will be assumed to be encoded in a numeric form such as $Healthy=0$, $Warning=1$, etc.

## Serial Correlation

Consider the following thought experiment:

```{note} Thought Experiment 1
Every day for a year, you flip a coin and record the date and either $-1$ (tails) or $1$ (heads).
```

By the strict definition given above, because this is indexed by time it qualifies as a time series. However, in the context of business analysis and data science, we generally mean something more when we refer to a set of observations as a "time series." We will only consider a series to be a time series if the past has predictive value, often (but not always) due to causation. Knowing a full year of coin flips for a fair coin provides no additional ability to predict the next flip—though a strong preponderance of one result may force us to re-evaluate our priors regarding the fairness of the coin.

```{note} Thought Experiment 2
You start a running tally at $0$. Every day for a year you flip a coin and either subtract $1$ (tails) or add $1$ (heads) and record the date and new tally.
```

In this case, knowing the previous day's value *will* help you predict the following value. For example, given that the current value is 8, we know that the next value must either be 7 or 9; put slightly differently, the series exhibits *serial correlation* meaning that the observation at time $t$ is highly correlated with the observation at time $t-1$, slightly less correlated with the observation at $t-2$, and so on. We will see in later chapters that this is an example of a *random walk*. 

Other cases may not be as clear-cut. Imagine we are working at a medical school analyzing patient statistics such as blood pressure, heart rate, and temperature. Let's imagine we analyze these records for a week; is this a time series? What about a year? What about 50 years? It seems unlikely that a single week of data will contain any useful trends or predictive value stemming from its ordering. On the other hand, 50 years of data will almost certainly contain valuable data on health trends and the direction we can expect vitals to take in the future.

So where do we draw the line as to what is or is not a time series? As with so much of data science, this question involves not only statistics, but *domain expertise*. There is no general rule, as a practicing data scientist you need to work with experts in the field to make an informed decision for your use case and goals.

:::{tip} Problem
Go to a newspaper or news site and find a series that could be considered a time series. Do they treat it as such? Are the variables numeric, categorical, or something else?
:::