---
layout: post
date: 2019-10-03 12:00:00
tags: ["Analysis", "Regression"]
title: "Linear regression for assessing and predicting the market value of used cars"
description: "This post applies linear regression to understand and predict the market value of 
used cars. The data used to build the models is scrapped from major car-listing websites.
The analysis and conclusions are purely meant to show how to apply linear regression to 
a real-world problem."
comments: true
---

## Introduction

## The theory

Linear regression inherently assumes that there is a linear relationship between a 
random variable (a.k.a the dependent variable) and another set of random variable(s)
(a.k.a the independent variables), i.e,

$Y = a_0 + a_1X_1 + ...+ a_dX_d + e$,

where a_0 is called the intercept and a_i is the coefficient for X_i. 

Linear regression acknowledges the relationship between the dependent and independent 
random variables can be non-deterministic. That is, there can be some uncertainity 
involved in determining the value of Y evenif we have a perfect knowledge of the values 
of the X_is. This uncertainity is represented by the error term e in the regression 
equation. All the uncertainity we cannot (or don't want to) explain about Y is absorbed 
by this term.

In order to build reliable models we make the following assumptions on the error term:

- The error e should be independent of X where X is the vector [X_1 ... X_d]. For example,
let our linear regression model represent the power consumption of an electronic device 
as a function of the load - P = P_0 + a L + e (P and L being the Power and Load random variables).
Then, the probabilistic nature of e should remain unaffected whether the Load L is 10% 
or 95%.

- The spread of e remains constant across all values of X. Going back to the previous example,
e should not be more (or less) spread for the range of load values under study.

- If statistical tests are required, we further need to assume that e has a normal
distribution centered at 0. For further details on related matter check the [assumptions](https://statisticsbyjim.com/regression/ols-linear-regression-assumptions/) 
and [best practices](https://towardsdatascience.com/how-do-you-check-the-quality-of-your-regression-model-in-python-fa61759ff685) 
of linear regression. 

The coefficients in a linear regression model can be solved directly by
[matrix inversion](https://en.wikipedia.org/wiki/Ordinary_least_squares) or more iteratively 
by [gradient descent](https://en.wikipedia.org/wiki/Gradient_descent) or its variants.

Here it is important to note that the 'linear' in linear regression does not necessarily 
mean the relationship between X and Y is expressed as a straight line. For example, 

Y = a_0 + a_1(X_1)^2 + a_2Log(X_2)

is also considered a linear model as long as the expression remains linear in the 
coefficients.

Clearly, there is a plethora of choices for modeling the relationship between Y & X. Thus 
we need to have metrics that quantify how well a model performs in capturing this relationship
to be able to select the best model. Two of such metrics are:

- R<sup>2</sup>: Also refered to as the [coefficient of determination](https://en.wikipedia.org/wiki/Coefficient_of_determination)
indicates how much of the variability of Y is explained by X (the independent variable). R<sup>2</sup> of 
1 tells us X perfectly (100%) explains the variability in Y, i.e., there is no uncertainty left in Y once 
the values of X are known. By contrast, R<sup>2</sup> of 0 suggests X does not explain any of 
the variability in Y at all. Other values between 0 and 1 lie on the spectrum accordingly.

- Predicted R<sup>2 and Adjusted R<sup>2: One weakness of R<sup>2 is that it is susiptible to 
overfitting. Predicted R<sup>2 and Adjusted R<sup>2 are aimed at mitigating this issue. Read
[this](https://statisticsbyjim.com/regression/interpret-adjusted-r-squared-predicted-r-squared-regression/)
post for more detail.

- RMSE: The Root Mean Squared Error is a single number summarizing how 'far', on average, the predictions 
of a model are from the actual values observed. The square of the error term e is summed accross multiple 
observations, divided by the number of observations to obtain the average Sum of Squares. 
Taking a square-root of this average (bringing the error to the same units as Y) 
gives the RMSE. RMSE = 0 represents a perfect model but there is no upper bound on how worse
the model can be.

## The data
- source & python code
- Type of cars 
- Summary statistics 
	-- total after removing missing values
	-- total after removing duplicates
	-- Per make-model summary

## Analysis

## Conclusion

<!--
<center><img src="{{ site.baseurl }}/assets/img/resource-group-arch.png" align="middle" style="width: 500px; height: 300px" /></center>
-->



