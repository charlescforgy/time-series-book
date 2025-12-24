# What is a Time Series?

## Data Type

By the strict mathematical definition, a time series is defined as a series of observations ordered by time. Note that we have not defined the observations as *numeric* values, a time series can be any sort of observation, including not only numeric data such as stock prices or infection rates, but also observations such as:
- Images indexed by time (i.e. videos)
- System health (healthy, warning, etc.)
- User sentiment on social media
- The geographic location of IP addresses visiting our website 

While the above are all examples of time series this text will focus on purely numeric data. Non-numeric data will be assumed to be encoded in a numeric form such as $Healthy=0$, $Warning=1$, etc.

## Serial Correlation

Consider the following thought experiment:

**Thought Experiment 1**

    Every day for a year you flip a coin and record the date and either -1 (tails) or 1 (heads).

By the strict definition given above, this is indexed by time and therefore qualifies as a time series. However, in the context of business analysis and data science, we generally mean something more when we refer to a set of observations as a "time series." We will only consider a series to be a time series if the past has predictive value, often (but not always) due to causation. Knowing a full year of coin flips for a fair coin provides no more ability to predict the next flip---though a strong preponderance of one result may force us to re-evaluate our assumptions regarding the fairness of the coin.

**Though Experiment 2**

     You start a running tally at 0. Every day for a year you flip a coin and either subtract 1 (tails) or add 1 (heads).

In this case, knowing the previous day's value will help you predict the following value. For example, given that the current value is 8, we know that the next value must either be 7 or 9, or the observation at time $t$ is highly correlated with the observation from $t-1$, and slightly less correlated with the observation from $t-2$, etc. We will see in later chapters that this is an example of a *random walk*. 

Other cases may not be as clear-cut. Imagine we are analyzing patient statistics at a hospital such as blood pressure, heart rate, and temperature. Let's imagine we analyze these records for a week; is this a time series? What about a year? What about 50 years? It seems unlikely that a single month of data will contain any useful trends or predictive value due to its ordering. On the other hand, 50 years of data almost certainly contains valuable data on health trends and the direction we can expect vitals to take in the future.

So where do we draw the line as to what is or is not a time series? As with so much of data science, determining if data should be treated as a time series involves not only statistics, but *domain expertise*. There is no general rule, as a practicing data scientist you need to work with experts in the field to make an informed decision for your use case and goals.
