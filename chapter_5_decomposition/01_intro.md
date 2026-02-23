# 5.1 Introduction

In this chapter we will cover the extremely useful tool *time series decomposition*. Decomposition is primarily used for time series analysis, though we will see methods in subsequent chapters to use decomposition as part of a larger forecasting algorithm. As a practicing data scientist you will often be tasked with helping your client or employer understand historical activity with an eye towards identifying both long-term trends and seasonal effects. Time series decomposition—in particular the algorithm known as *STL decomposition*—should generally be among your first tools to answer such questions.

We will begin this chapter by discussing classical decomposition methods developed in the 1920s and 1930s. We cover classical decomposition not because it is the best method currently available (it isn't), but to lay the foundations to understand more sophisticated methodologies.

We will then take a detour to discuss local regression before combining classical decomposition with local regression in the STL decomposition algorithm first published in 1990. STL decomposition is arguably the single most versatile and flexible algorithm for time series decomposition, and is my recommended method for most scenarios.

Note that we will not be covering methods such as the X-11 family. This family of models was first introduced by the US Census Bureau in 1967, with regular updates and refinements since then[^1]. While valuable tools for their intended purposes, the X-11 family is fairly specific to the needs of government statisticians and is not widely adopted elsewhere. STL decomposition is both more flexible in terms of seasonal effects and more widely adopted, and should generally be favored for scenarios other than specific government statistics cases.

[^1]: The newest version is [X-13ARIMA-SEATS](https://www.census.gov/data/software/x13as.html) introduced in 2020 and available for download free of charge at the link provided.