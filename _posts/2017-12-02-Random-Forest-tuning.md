---
layout: post
title: Random Forest Tuning
---

&emsp;&emsp;Over the past 2 years since learning Python/R from zero, I have been forcing myself to learn how to build an algorithmic trading program. It has definitely been a very steep curve but as with everything else, a challenege is what I enjoy. It definitely took a lot of reading, trading strategies but also improving my programming skills in general. I remember how much I forced myself to do countless variations "Hello World" just to learn print functions. Now, I share my knowledge as much as I can with other people who are interested, either programming in general, infosec or data science.      

&emsp;&emsp;In this post, we will look at parameter tuning for random forest for mtry and trees. It will follow the standard grid search but deal with the "typical values" as compared to actual data. In this case, I am performing a regression modelling for stocks/shares in the Kuala Lumpur Stock Exchange (KLSE). 

> What is mtry? 
> Mtry is the number of randomly chosen variables/features when forming a split in the tree. If the data has 60 different features/predictors, the model will select a subset of it during splitting and find the feature which reduces the node impurity the most or best explains the data (in simple terms, least error). 
> This is in comparison to a standard recursive partitioning algorithm where it searches through all the features, instead of a subset, and finds the feature which best describes the data. 
> Essentially is the amount of features made available during splitting.

> What are trees?
> These are typically the depth (vertical depth) of the decision trees which are built to describe the data based on the variables. It can also be thought of as the levels of splits. 

<br>

Here is a [deeper explanation at Analytics Vidhya][1] 
<br>

Typical advice you will hear when working with random forests is 
* build deep trees until the magnitude of improvement for error tapers down 
* start with investigating the values around mtry = sqrt(features)
<br><br>

Some details of R packages I am using:
* ranger
* caret - *postResample()* to measure RMSE and Rsquared

&emsp;&emsp;Generally, when we start tuning these parameters we essentially hold either one value constant and iterate over the other. In the figure below, that is exactly what I have done, set the number of trees at 150 and look at varying values for mtry where *mtry = sqrt(features) = 8*.


![_config.yml]({{ site.baseurl }}/images/2017-12-02/wprts_mtry_5-13_analysis.png

&emsp;&emsp;From the figure, it is obvious that mtry=12 is the best since it gives the lowest value for RMSE around 0.072. However, the overall Rsquared is very low around 0.45. If we were to stick with the "typical advice" then the solution would have been to choose mtry=12 and continue on. However, being curious as usual, I decided to iterate mtry at the higher extreme where having all the possible features available for consideration. 

![_config.yml]({{ site.baseurl }}/images/2017-12-02/wprts_mtry_40-57_analysis.png

&emsp;&emsp;As is noticeable, even the range for RMSE has dropped to below 0.07 and the Rsquared has climbed to higher than 0.5. So it seems that having all, if not most, of the features considered at each split improves the model. 

&emsp;&emsp; Given that I will be modelling for all the stocks on the KLSE main board, I will choose mtry between 50 and 57. 
<br><br>

Next, we iterate over the trees at constant value of mtry to determine the best depth. 











[1]: https://www.analyticsvidhya.com/blog/2016/04/complete-tutorial-tree-based-modeling-scratch-in-python/

