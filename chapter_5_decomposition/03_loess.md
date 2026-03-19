# 5.3 Local Regression

## Kernel Smoothing

## LOWESS Algorithm

Local regression is perhaps best thought of as a hybrid between kernel smoothing and linear regression (or more broadly polynomial regression). The most common family of methods is LOWESS (locally weighted scatterplot smoothing), a type of LOESS (locally estimated scatterplot smoothing)[^1]. The LOWESS algorithm is generally credited as having been formally introduced in [](https://doi.org/10.1080/01621459.1979.10481038) and [](https://doi.org/10.2307/2683591) (though much of the concept had been proposed seperately by [](https://doi.org/10.1021/ac60214a047))

[^1]: Some sources such as [NIST](https://www.itl.nist.gov/div898/handbook/pmd/section1/pmd144.htm) treat the terms LOESS and LOWESS as being interchangeable. Cleveland himself in [](https://doi.org/10.1080/01621459.1988.10478639) appears to have used the term LOESS as a multivariate generalization of the previous univariate LOWESS.

<html><iframe src="../static/lowess_demo.html" width="100%" height="520" frameborder="0"></iframe></html>