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
** Thought Experiment **
Every day for a year you flip a coin and record the date and either 0 (tails) or 1 (heads).

By the strict definition given above, this is indexed by time and therefore qualifies as a time series. However, in the context of business analysis and data science, we generally mean something more when we refer to a set of observations as a ``time series."
