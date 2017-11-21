---
layout: post
title: McKinsey Analytics Online Hackathon
---



&emsp;&emsp;Over the weekend, I joined my first hackathon organized by [McKinsey and hosted on AnalyticsVidhya][1]. The hackathon was over a 24-hour period starting from 11am AEST Saturday and finishing the next day. The event itself was a hiring hack and includes the prize of an all-expense paid trip to any international analytics conference of choie as a McKinsey guest. 

&emsp;&emsp;To be honest, I don't remember how I found out about this event but I just signed up without thinking much and was not really planning on participating as I knew my weekend was full. Initially, the thought was just to see what kind of problem would be presented.

*This post will focus on junction1 (one of four junctions) and describe the thinking at that time. During the event, a lot of time was spent exploring the data before even beginning to model. The steps here are definitely not optimal as time was very limited, so the initial focus was to be able to output a realistic result and see how it ranks.* 


Jumping onto the site after a lunch engagement, the problem was as below. 
> ### Problem Statement
> #### Mission
>
>You are working with the government to transform your city into a smart city. The vision is to convert it into a digital and intelligent city to improve the efficiency of services for the citizens. One of the problems faced by the government is traffic. You are a data scientist working to manage the traffic of the city better and to provide input on infrastructure planning for the future.
>
>The government wants to implement a robust traffic system for the city by being prepared for traffic peaks. They want to understand the traffic patterns of the four junctions of the city. Traffic patterns on holidays, as well as on various other occasions during the year, differ from normal working days. This is important to take into account for your forecasting. 
>
> #### Your task 
>
>To predict traffic patterns in each of these four junctions for the next 4 months.
> Time to apply your data science skills. Good luck!

<br>

Seeing the words predict definitely got me going so I peeked at the train dateset.

{% highlight r %}
DateTime,Junction,Vehicles,ID
2015-11-01 00:00:00,1,15,20151101001
2015-11-01 01:00:00,1,13,20151101011
2015-11-01 02:00:00,1,10,20151101021
2015-11-01 03:00:00,1,7,20151101031
2015-11-01 06:00:00,1,9,20151101061
2015-11-01 00:00:00,2,15,20151101001
2015-11-01 01:00:00,2,13,20151101011
2015-11-01 02:00:00,2,10,20151101021
2015-11-01 03:00:00,2,7,20151101031
2015-11-01 00:00:00,3,15,20151101001
2015-11-01 01:00:00,3,13,20151101011
2015-11-01 02:00:00,3,10,20151101021
2015-11-01 03:00:00,3,7,20151101031
2015-11-01 00:00:00,4,15,20151101001
2015-11-01 01:00:00,4,13,20151101011
2015-11-01 02:00:00,4,10,20151101021
2015-11-01 03:00:00,4,7,20151101031
...
{% endhighlight %}
And here is a snippet the test dataset. 

{% highlight r %}
DateTime,Junction,ID
2017-07-01 00:00:00,1,20170701001
2017-07-01 01:00:00,1,20170701011
2017-07-01 00:00:00,2,20170701001
2017-07-01 01:00:00,2,20170701011
2017-07-01 00:00:00,3,20170701001
2017-07-01 01:00:00,3,20170701011
2017-07-01 00:00:00,4,20170701001
2017-07-01 01:00:00,4,20170701011
...
{% endhighlight %}

<br>
&emsp;&emsp;Ooh! Time-series data! This got me really excited, considering that I have time until my dinner engangement, I fired up RStudio. Drawing from my experience, this could go two ways either similar to share/forex prices (stochastic) or similar to building energy data (pattern based on hour of day, day of week etc.). 

<br>
Looking at the data itself, the following is obvious:
* ID is just a concatenation of the date, hour and junction
* The only features seems to be the date and time
Based on the rules, we are not to infer any other type of information outside of the given dataset, i.e. don't assume holidays which are country specific.

&emsp;&emsp;After reading in the data, first was to make the junction as a factor so that the data can be split into 4 different dataframes. Next, convert datetime strings to POSIX using the lubridate package, the time series features are extracted using the tk_augment_time_series from the package timetk (previously known as timekit). The functions extracts so many different layers of information from the datetime string. This should be followed by the usual *str()* and *summary()* to review the data.

&emsp;&emsp;*[Read more about timetk.]*[2] 

&emsp;&emsp;Before we proceed it is important to remember that just because the data contains timestamps, does not necessarily mean it is the correct timestamp. The timestamp could be in UTC where the data was collected in another part of the world. This is sometimes the case when working with time series data.

{% highlight r %}
# separate data into junctions
dfjunc <- lapply( levels(traindata$Junction), function(k){
                  traindata[ which(traindata$Junction == k ) ,]} )
dfjunc <- setNames(dfjunc, seq(1,4) )

# format datetime and extract information
dfjunc$'1'$DateTime <- ymd_hms(dfjunc$'1'$DateTime)
dfjunc1 <- tk_augment_timeseries_signature(dfjunc$`1`)

names(dfjunc1)

[1] "DateTime"  "Junction"  "Vehicles"  "ID"        "date"   
[6] "index.num" "diff"      "year"      "year.iso"  "half"   
[11]"quarter"   "month"     "month.xts" "month.lbl" "day"    
[16]"hour"      "minute"    "second"    "hour12"    "am.pm" 
[21]"wday"      "wday.xts"  "wday.lbl"  "mday"      "qday"  
[26]"yday"      "mweek"     "week"      "week.iso"  "week2"  
[31]"week3"     "week4"     "mday7" 
{% endhighlight %}


<br><br>
Look at all the features extracted just from the datetime string. Next up was plotting the time series data itself. 
![_config.yml]({{ site.baseurl }}/images/2017-11-20-timeseries.png)

![_config.yml]({{ site.baseurl }}/images/2017-11-20-timeserieszoom.png)


<br>
&emsp;&emsp;From here, I noted the shape of traffic daily and there seems to be a pattern for different days of the week. This intuition is something I picked up from my previous role working with building energy consumption. In that role, an additional feature was the outside air temperature. Essentially, if we were to plot a 3-dimensional surface, the x and y axes would be hour of day and increasing time (which can be represented by POSIX time in the *index.num* column) with the dependent variable being number of vehicles.

Note the following:
* increasing trend over time (maybe population growth? cheap car loans? cheap cars?)
* some spikes at points (possibly other road closures?)
* a dip over the christmas and new year period in 2017 (interestingly 2016 had barely noticeable effect)

<br>
&emsp;&emsp;Before continuing here, I opened another file in the editor (pipeline.R)  and start thinking about the pipeline. Building processes as we go along is the best way to make sure it is documented and ideas are not forgotten or lost. 

Start the pipeline with a simple flow:
> Initialize >> Read data >> ...

&emsp;&emsp;As we understand the data in EDA (exploratory data analysis), we can update this file either with code or just with simple comments. 

<br>
Moving on, I plotted some boxplots across the hours, days of the week and day of months. 
![_config.yml]({{ site.baseurl }}/images/2017-11-20-hourofday.png)

![_config.yml]({{ site.baseurl }}/images/2017-11-20-dayofweek.png)

![_config.yml]({{ site.baseurl }}/images/2017-11-20-dayofmonth.png)

&emsp;&emsp;Note the outliers from the boxplots. It is expected to have outliers at the top where the traffic does spike but for the bottom part which we observe for christmas and new year, it is not observable below since it is mixed with other data. 

&emsp;&emsp;At this point, it did not seem that the boxplots provided much value since they do not represent an accurate picture. Again, just look at hour between 10am and 7pm from the "Hour of day" figure, it is easy to understand that since the data is increasing over time, even the outliers are possibly just data at the later end of the time scale. 

&emsp;&emsp;An interesting point to note is the small range of data in the early hours of the morning, suggesting that despite a large increase during the day the traffic at this hour has not significantly increased. 

<br>
The plot of change over week describe the intuition at the time.
&emsp;*Note the plot below was created post event to explain this better*

![_config.yml]({{ site.baseurl }}/images/2017-11-20-changeoverweek.png)


<br>
&emsp;&emsp;Progressing further and to confirm the intuition, I stepped through the hours of every weekday. The plot below shows the vehicles over time for the every 5pm of every Monday. 

![_config.yml]({{ site.baseurl }}/images/2017-11-20-stephourweekday.png)

&emsp;&emsp;It is immediately obvious that the data is almost linear here. It would be easy to model at this granularity, so model and look at residual without forgetting to remove unnecessary columns that are not continuous variables such as labels and repetitive values ( e.g. wday and wday.xts).

&emsp;&emsp;Normally at this point, I would look at variable correlations prior to building a model but in the moment, I had limited time and wanted to get at least one submission in. The priority here was to think about the pipeline to make it easy to iterate. So, I made a note and if I had time, I would come back to it. 

![_config.yml]({{ site.baseurl }}/images/2017-11-20-actualvsfitted values.png)

Next, to assess the residuals, create the figure below.

![_config.yml]({{ site.baseurl }}/images/2017-11-20-residuals1.png)

&emsp;&emsp;It is clear that there are several outliers from the model. This could easily be as a result of a poor model which is known to be the case since the features were not filtered properly. Refocusing again on the pipeline, just creates some filters to remove the outliers as with better feature selection, this would already be automated. 

![_config.yml]({{ site.baseurl }}/images/2017-11-20-actualvsfittedvalues2.png)

This looks better, now assess residuals. 

![_config.yml]({{ site.baseurl }}/images/2017-11-20-residuals1.png)

&emsp;&emsp;All the outliers have been removed. The data looks to be normally distributed too from the QQ plots. 


[1]: https://datahack.analyticsvidhya.com/contest/mckinsey-analytics-hackathon/
[2]: https://rdrr.io/cran/timetk/f/README.md
<br><br>
