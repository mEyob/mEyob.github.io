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

### 1. Introduction

The purpose of this blog post is to explore the possibility of applying linear regression to 
describe, analyze and predict
the listing price of used cars. In this regard, linear regression is introduced more loosely 
without having to define concepts in a strict mathematical sense. The data used in this post
is scrapped from major used car listing websites. For more on the data see the [Data section](#3-data).
You can also checkout my [Github](https://github.com/mEyob/linear-regression-car-price) page if 
you are interested in the python code used for scrapping or if you want to download the data in csv format.

To make like-for-like comparisons and analysis, the following mid-sized sedans are considered.

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

The goal is to be able to address questions like:

-  How much would a model X car that is driven Y miles cost?	
-  What is the average price of model X cars after Y miles of usage?
-  How much does depriciation (w.r.t. mileage, release year) affect model A cars compared to model B?

That said, everything stated in this blog post is solely based on curiosity and the desire
to apply regression to the used car market. Obviously, anyone seriously considering to buy a car
should seek expert advice.

### 2. The theory

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

### 3. The data

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

<center><img src="{{ site.baseurl }}/assets/img/cars-summary-stat.png" align="middle" style="width: 300px; height: 400px" /></center>

The CSV file contains listing details of 11,018 cars. However, dealers may list the same 
car more than once to improve its chance of being discovered among other cars. Duplicate entries 
may lead to inaccurate analysis and prediction. We, therefore, need to remove duplicates and run
the summary statistics again. Doing so reveals that about half of the listings are duplicate entries
as shown below.

<center><img src="{{ site.baseurl }}/assets/img/cars-summary-stat-nodup.png" align="middle" style="width: 300px; height: 400px" /></center>

Now we know that there are 6396 unique cars. Going one level deeper, we can see the number of cars, 
the average price, average mileage and average release year per manufacturer (model) below

<center><img src="{{ site.baseurl }}/assets/img/per-manufac-stat.png" align="middle" style="width: 400px; height: 500px" /></center>

Out of the 6396 unique cars listed, 1540 are Nissan Altima, 1317 are Toyota Camry, 1058 are Honda Accord
and so on.

### 4. Analysis

Here, the goal is to build a multi-linear regression model that predicts (summarizes) the 
*Listing Price* of a used mid-size sedan car based on its *Mileage*, *Release (Model) Year*, and *Trim level*.
We'll build separate regression models for each of the cars listed above, excluding VW Passat 
and Chevrolet Malibu. 

Before jumping into building the regression models, let us examine how *Price* tends to change 
with respect to *Mileage* and *Year* by taking the Honda Accord data set as an example. The following plots reveal 
the strong negative relationship between mileage and price and the strong positive relationship between release year and price.

<center><img src="{{ site.baseurl }}/assets/img/honda-trend-curves.png" align="middle" style="width: 800px; height: 500px" /></center>

The correlation matrix shown below quantifies this relationship. It also shows the strong negative relationship 
between Mileage and Year. Intuitively, it is highly unlikely for a 2019 model to rack up 100,000 miles
as it is equally unlikely for a 2008 model to have a mileage of 10,000 miles.

<center><img src="{{ site.baseurl }}/assets/img/honda-corr.png" align="middle" style="width: 300px; height: 250px" /></center>

For more on this and other analysis related to regression model building see [this](https://github.com/mEyob/linear-regression-car-price/blob/master/analysis.ipynb) python notebook.

#### 4.1 Regression models
After considering a number of features and feature transforms the following regression function that better expresses the relationship between the independent variables (*Mileage*, *Year*, *Trim level*)and dependent variable (*Listing Price*)is  chosen:

$$P_i(M_i, Y_i, T_i) = a_0 + a_1 log2(M_i) + a_2 (2019 - Y_i) + \sum_{j=2}^{n} a_{j+1} 1_{\{T_j=T_i\}}$$,

where $$P_i,M_i, Y_i, \text{ and }T_i$$ are the *Price, Mileage, Year,* and *Trim* of car $$i$$, respectively.
$$a_0, a_1, a_2$$ are the intercept, coefficient w.r.t *Mileage*, and coefficient w.r.t. *Year*.
Since trim level is a categorical variable, we need to [dummy-encode](https://en.wikiversity.org/wiki/Dummy_variable_(statistics)) it before it can be used in a regression model. Assuming there are $$n$$ trim levels of a car model $$n-1$$ dummy variables are 
created using the summation of the indicators in the last term of the above equation. 
This results in one dummy variable for each trim level except the base trim-level. Coefficeint $$a_{j+1}$$,
therefore, represents the price difference between the base trim level and the $$j^{th}$$ trim level.

Note that *Mileage* and *Year* are transformed using functions $$f(M)=log2(M)$$ and $$g(Y) = 2019 - Y$$.
The log transformation of Mileage not only yields better model performance it can also be motivated by the 
fact that the sharpest decline in Price happens during the first few (thousand) miles. Once a car makes it
into tens of thousands of miles, the rate at which the Price decreases should slow down. Logarithmic 
functions are one of several functions that could capture this relationship between *Mileage* and *Price*.
Because the logarithm is in base $$2$$, $$a_1$$ is how much the value of a car decreases everytime 
Mileage doubles

When it comes to the *Year*, what affects the *Price* of a car is how old the model is relative to 
the newest model, which is why the transformation $$g(Y) = 2019 - Y$$ is used. For example, a $$2016$$
Toyota Camry is $$3$$ years old and should have a lower price than a $$2017$$ ($$2$$ years old) Toyota
Camry if everything else is kept constant. 

Five elastic net regularised models are trained for Ford Fusion, Honda Accord, Hyundai Sonata, Nissan Altima,
and Toyota Camry. The following table provides the coefficients and performance metrics.

|--------------|-----|------------------------------|-------------------|---------|
|Car Model     |Count|Coefficients($$a_1,a_2$$)     |$$R^2$$<sup>+</sup>| RMSE($) |
|--------------|-----|------------------------------|-------------------|---------|
|Ford Fusion   |743  |     (-1180, -920)            |   81.2%           | 1543    |
|Honda Accord  |1015 |     (-1350, -1050)           |   87.2%           | 1888    |
|Hyundai Sonata|687  |     (-1200, -770)            |   80.3%           | 1302    |
|Nissan Altima |1434 |     (-1250, -780)            |   70.9%           | 1217    |
|Toyota Camry  |1172 |     (-770, -1010)            |   77.2%           | 1510    |
|              |     |                              |                   |         |

*+* $$R^2$$ score is calculated on a test set after outlier removal using [Cook's distance](https://en.wikipedia.org/wiki/Cook%27s_distance)

According to the regression models, Toyota Camry has the smallest (in magnitude) coefficient w.r.t
*Mileage* at $$a_1=-770$$. Take, for example, the price of a given Toyota Camry is $$\$20,000$$ at 500 miles. 
The model predicts that the average price drop follows the trend $$\$19,230$$ at $$1000$$ miles, $$\$18,460$$ at
$$2000$$ miles, $$\$17,690$$ at $$4000$$ miles and so on.

Note that coefficient $$a_1$$ represents the **depreciation with respect to mileage**, which makes Toyota Camry 
the best one among the five models considered for retaining much of its original value as mileage increases.
By contrast, Nissan Altima and Hyundai Sonata exhibit the least **depreciation with respect to how old the 
car model is** with every passing year knocking off about $$\$800$$ of the value of a car.

The following plots show the fitted regression models for all car models along with the data used to fit 
the regression models. In order to simplify the plots into 2D, cars (points in the scatter plot) are 
assumed to rack up [12000 miles per year](https://www.carinsurance.com/Articles/average-miles-driven-per-year-by-state.aspx), allowing us to represent *Year* in terms of *Mileage* as $$Year = Mileage // 12000$$.

<center><img src="{{ site.baseurl }}/assets/img/regfit-all.png" align="middle" style="width: 500px; height: 600px" /></center>

The bottom right plot shows all five regression curves in one plot. This plot, once more, gives the visual
confirmation that Toyota Camry loses the least amount of value to depreciation.

Without the 12000 miles/year assumption, the resulting plots are 3-dimensional. The next figure 
shows such a plot for Honda Accord. It is easy to see that *Price* decreases linearly in the *Year*
axis and logarithmically in the *Mileage* axis.

<center><img src="{{ site.baseurl }}/assets/img/honda-3d.png" align="middle" style="width: 600px; height: 600px" /></center>

#### 4.2 Prediction and comparison



### 5. Conclusion

<!--

	- start with simple model:
	- map car-price model to X, Y and A
	- mention standardizing 
	- Check assumptions: random residuals that are indpendent of the observations
	- examine R2 and RMSE 
- Add more feature(s) and/or transformations

-->



