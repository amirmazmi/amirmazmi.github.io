---
layout: post
title: McKinsey Analytics Online Hackathon
---



&emsp;&emsp;Over the weekend, I joined my first hackathon organized by [McKinsey and hosted on AnalyticsVidhya][1]. The hackathon was over a 24-hour period starting from 11am AEST Saturday and finishing the next day. The event itself was a hiring hack and includes the prize of an all-expense paid trip to any international analytics conference of choie as a McKinsey guest. 

&emsp;&emsp;To be honest, I don't remember how I found out about this event but I just signed up without thinking much and I was not really planning on participating as I knew my weekend was full. Initially, it was just to see what kind of problem would be presented. 

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

```
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
```
And here is a snippet the test dataset. 

```
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
```
&emsp;&emsp;Ooh! Time-series data! This really got me excited, considering that I have time until my dinner engangement, I fired up RStudio. Drawing from my experience, this could go two ways either similar to share/forex prices (stochastic) or similar to building energy data (pattern based on hour of day, day of week etc.). 

Looking at the data itself, the following is obvious:
* ID is just a concatenation of the date, hour and junction
* The only features seems to be the date and time

Based on the rules, we are not to infer any other type of information outside of the given dataset, i.e. don't assume holidays which are country specific. 

&emsp;&emsp;After reading in the data, first the convert to POSIX using the **lubridate** package, the time series features are extracted using the **_tk_augment_time_series_** from the package **timetk** (previously known as timekit). The functions extracts so many different layers of information from the datetime string.

```

```









<!-
!Next you can update your site name, avatar and other options using the _config.yml file in the root of your repository (shown below).

![_config.yml]({{ site.baseurl }}/images/config.png)

!The easiest way to make your first post is to edit this one. Go into /_posts/ and update the Hello World markdown file. For more instructions head over to the [Jekyll Now repository](https://github.com/barryclark/jekyll-now) on GitHub.




->


[1]: https://datahack.analyticsvidhya.com/contest/mckinsey-analytics-hackathon/

