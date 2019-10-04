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

## 1. Introduction

The purpose of this blog post is to show how linear regression can be applied to analyze 
the listing price of used cars. In this regard, linear regression is introduced more loosely 
without having to define concepts in a strict mathematical sense. The data used in this post
is scrapped from major used car listing websites. For more on the data see the [Data section](#3-The&data).
You can also checkout my [Github](https://github.com/mEyob/linear-regression-car-price) if 
you are interested in the python code used for scrapping or to access the data in csv format.

To make like-for-like comparisons and analysis, used cars from the following manufacturers
and models are considered

<table style="width:50%">
<tr>
    <th>Manufacturer</th>
    <th>Model</th>
  </tr>
  <tr>
    <td>Toyota</td>
    <td>Camry</td>
  </tr>
  <tr>
    <td>Honda</td>
    <td>Accord</td>
  </tr>
  <tr>
    <td>Nissan</td>
    <td>Altima</td>
  </tr>
    <tr>
    <td>Ford</td>
    <td>Fusion</td>
  </tr>
    <tr>
    <td>Hyundai</td>
    <td>Sonata</td>
  </tr>
    <tr>
    <td>Chevrolet</td>
    <td>Malibu</td>
  </tr>
    <tr>
    <td>Volkswagen</td>
    <td>Passat</td>
  </tr>

</table>
<!--
| Manufacturer| Model   |
| ------------| ----    | 
|Toyota       |    Camry|
|Honda        |   Accord|
|Nissan       |   Altima|
|Ford         |   Fusion|
|Hyundai      |   Sonata|
|Chevrolet    |   Malibu|
|Volkswagen   |   Passat|
-->


The goal is to be able to address questions like:

-  How much would a model X car that is driven Y miles cost?	
-  What is the average price of model X cars after Y miles of usage?
-  How much does depriciation (w.r.t. mileage, release year) affect model A cars compared to model B?

That said, everything stated in this blog post is solely based on curiosity and the desire
to apply regression to the used car market. Obviously, anyone seriously considering to buy a car
should seek expert advice.

## 2. The theory

Linear regression inherently assumes that there is a linear relationship between a 
random variable (the dependent variable) and another set of random variable(s)
(the independent variables), i.e,

$$Y = a_0 + a_1X_1 + ...+ a_dX_d + e$$,

where $$a_0$$ is called the intercept and $$a_i$$ is the coefficient for $$X_i$$.
Often, the independent variable and the coefficients are expressed as $$d+1$$-dimensional
vectors, i.e., $$X=[1 X_1 X_2 ... X_d]$$ and $$A=[a_0 a_1 ... a_d]$$ 

Linear regression acknowledges the relationship between the dependent and independent 
random variables can be non-deterministic. That is, there can be some uncertainity 
involved in determining the value of Y evenif we have a perfect knowledge of the values 
of the $$X_i$$s. This uncertainity is represented by the error term e in the regression 
equation. All the uncertainity we cannot (or don't want to) explain about Y is absorbed 
by this term.

In order to build reliable models we make the following assumptions on the error term:

- The error $$e$$ should be independent of $$X$$. For example,
let our linear regression model represent the power consumption of an electronic device 
as a function of the load, and let us assume the model is $$P = P_0 + a L + e$$
($$P$$ and $$L$$ representing the $$Power$$ and $$Load$$ random variables, respectively).
The independence assumption dictates that the probabilistic nature of $$e$$ should remain 
unaffected whether the load is *10%, 95%*, or any other value.

- The spread of e remains constant across all values of X. Going back to the previous example,
e should not be more (or less) spread for the range of load values under study.

- If statistical tests are required, we further need to assume that e has a normal
distribution centered at 0. For further details on related matter check the [assumptions](https://statisticsbyjim.com/regression/ols-linear-regression-assumptions/) 
and [best practices](https://towardsdatascience.com/how-do-you-check-the-quality-of-your-regression-model-in-python-fa61759ff685) 
of linear regression. 

The coefficients in a linear regression model can be solved directly by
[*matrix inversion*](https://en.wikipedia.org/wiki/Ordinary_least_squares) or more iteratively 
by [*gradient descent*](https://en.wikipedia.org/wiki/Gradient_descent) or one of its variants.

Here it is important to note that the 'linear' in linear regression does not necessarily 
mean the relationship between X and Y is expressed as a straight line. For example, 

$$Y = a_0 + a_1(X_1)^2 + a_2Log(X_2)$$

is also considered a linear model as long as the expression remains linear in the 
coefficients.

Clearly, there is a plethora of models to choose from. 
we, therefore, need to have metrics that quantify how well a model performs in capturing 
the relationship between X & Y to be able to select the best model. Two of such metrics are:

- **R<sup>2</sup>**: Also refered to as the [coefficient of determination](https://en.wikipedia.org/wiki/Coefficient_of_determination)
indicates how much of the variability of Y is explained by X (the independent variable). R<sup>2</sup> of 
1 tells us X perfectly (100%) explains the variability in Y, i.e., there is no uncertainty left in Y once 
the values of X are known. By contrast, R<sup>2</sup> of 0 suggests X does not explain any of 
the variability in Y at all. Other values between 0 and 1 lie on the spectrum accordingly.

- **Predicted *R<sup>2</sup>* and Adjusted *R<sup>2</sup>***: One weakness of *R<sup>2</sup>* is that it is susiptible to 
overfitting. Predicted *R<sup>2</sup>* and Adjusted *R<sup>2</sup>* are aimed at mitigating this issue. Read
[this](https://statisticsbyjim.com/regression/interpret-adjusted-r-squared-predicted-r-squared-regression/)
post for more detail.

- **RMSE**: The *Root Mean Squared Error* is  the expected magnitude of prediction error. 
RMSE = 0 represents a perfect model, and model performance decreases as RMSE increases. 
RMSE can also be nicely decomposed into [bias and variance](https://www.youtube.com/watch?v=zrEyxfl2-a8) terms of the model.

We will also apply *confidence* and *prediction* intervals. [This](https://www.youtube.com/watch?v=gNIpR8bdgUo) video
elegantly shows what their difference is and how to calculate them.

## 3. The data

The data, as already mentioned in the introduction, is scrapped from car listing websites.
The scrapped data is parsed and cleaned and the resulting data is stored in a csv file, which
has the following format:


<table style="width:75%">
<tr>
    <th>Year</th>
    <td>Year of release, e.g. 2010, 2019</td>
</tr>
<tr>
    <th>Make</th>
    <td>Manufacturer of the car: Honda, Toyota, Nissan, Ford, Hyundai, Chevrolet and Volkswagen</td>
</tr>
<tr>
    <th>Model</th>
    <td>Accord, Camry, Altima, Fusion, Sonata, Malibu or Passat</td>
</tr>
<tr>
    <th>Model detail</th>
    <td>Trim level, e.g. for Honda LX, Sport, EX, EX-L and Touring</td>
</tr>
<tr>
    <th>Price</th>
    <td>Listing price of the car</td>
</tr>
<tr>
    <th>Mileage</th>
    <td>Mileage (in miles) posted by dealer</td>
</tr>
<tr>
    <th>Website</th>
    <td>A two letter abbreviation to identify the source website</td>
</tr>
</table>

The following table provides valuable summary statistics of the quantitative columns

<center><img src="{{ site.baseurl }}/assets/img/cars-summary-stat.png" align="middle" style="width: 300px; height: 500px" /></center>

The CSV file contains listing details of 11,018 cars. However, dealers may list the same 
car more than one times, hoping that it will remain on top of the list. Duplicate entries 
may lead to inaccurate analysis and prediction. Removing duplicates and running the summary
statistics again, we can see that about half of the listings are duplicate entries.

<center><img src="{{ site.baseurl }}/assets/img/cars-summary-stat-nodup.png" align="middle" style="width: 300px; height: 500px" /></center>

Going one level deeper, we can see the number of cars, the average price, average mileage and average release year 
per manufacturer (model) below

<center><img src="{{ site.baseurl }}/assets/img/per-manufac-stat.png" align="middle" style="width: 400px; height: 500px" /></center>

- source & python code
- Type of cars 
- Summary statistics 
	-- total after removing missing values
	-- total after removing duplicates
	-- Per make-model summary

## 4. Analysis

## 5. Conclusion

<!--
<center><img src="{{ site.baseurl }}/assets/img/cars-summary-stat.png" align="middle" style="width: 500px; height: 300px" /></center>
-->



