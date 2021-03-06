---
title: "Reproducible Research"
author: "John Atanga"
date: "7/7/2021"
output: 
  html_document: 
    keep_md: yes
---
## This is my document for the Assignment
## Load the data, in this case one call the read.csv():
## Process/ Transform the data inti a format suitable  for the analysis 

```r
if(!file.exists("getdata-projectfiles-UCI HAR Dataset.zip")) { 
  temp <- tempfile()
  download.file("https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip", temp)
  unzip(temp)
}
activity <- read.csv("activity.csv")
head(activity)
```

```
##   steps       date interval
## 1    NA 2012-10-01        0
## 2    NA 2012-10-01        5
## 3    NA 2012-10-01       10
## 4    NA 2012-10-01       15
## 5    NA 2012-10-01       20
## 6    NA 2012-10-01       25
```
## I will like to view some information about variables

```r
str(activity)
```

```
## 'data.frame':	17568 obs. of  3 variables:
##  $ steps   : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ date    : chr  "2012-10-01" "2012-10-01" "2012-10-01" "2012-10-01" ...
##  $ interval: int  0 5 10 15 20 25 30 35 40 45 ...
```
## The following variables are included in this dataset:
## 1. **steps** : Number od steps taken in 5-minutes interval( missing values are coded as NA)
## 2. **date**  : The date on which the measurement was taken in YYYY-MM-DD format
## 3. **interval** : Identifier for the 5-minute interval in which measurement was taken.

## I will Process and transform the data  into a format that is suitable for the analysis

## **What is the mean number of steps taken per day?**
## For this part of the assignment one can ignore  the  missing values in the dataset. 1. I will calculate the total number of steps taken per day.

```r
totalStepsByDay <- aggregate(steps~date, activity, sum)
```
## 2. I will make a histogram of the total number of steps taken each day


```r
hist(totalStepsByDay$steps, xlab = "Class of Total Number of Steps per day", ylab = "Number of Days", main = "Total Number of Steps taken each day")
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

## One can also calaculate and report the mean and median of the total number of steps taken per day


```r
mean_raw <- mean(totalStepsByDay$steps)
mean_raw
```

```
## [1] 10766.19
```
## Mean number of number of steps taken per day is 1.076618910^{4}

```r
median_raw <- median(totalStepsByDay$steps)
median_raw
```

```
## [1] 10765
```
## Median number of steps taken per day is 10765

## **What is the average daily activity pattern?**
## 1. One can make a time series plot ( type = "l") of the 5-minutes interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)
## The average number of steps taken:


```r
averageStepsbyInterval <- aggregate(steps~interval, activity, mean)
```

## Time series plot of the average number of steps taken:


```r
with(averageStepsbyInterval, plot(interval, steps, type = "l"))
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

## 2. One can determine which 5-minutes interval, on average across all the days in the dataset, that contains the maximum number of steps, is a good question, ready to go


```r
averageStepsbyInterval[which.max(averageStepsbyInterval[,2]), 1]
```

```
## [1] 835
```
## **Imputing missing values**
## There were a number of days/intervals with missing values - coded as NA. thus presenting bias.
## 1. One can calculate and report the total number of dmissing values in the dataset, ie NAs


```r
missingIndex <- is.na(activity[, 1])
```
## There are 2304 missing values in the dataset
## 2. Device a strategy for filling in all of the missing values in the dataset. use the mean / median for that day or for 5-minutes interval. 
## I used the mean number of steps perinterval to fill missing values
## Finding the mean number of steps per interval

```r
m <- mean(averageStepsbyInterval$steps)
```

## 3. One can create a new dataset that is equal to the original dataset but wiht the missing data filled in 
Imputing missing values  with M=37.3825996


```r
activity1 <- activity
activity1[missingIndex, 1] <- m
```

## Make a histogram of the total number of steps taken each day and calculate and report the mean an d median number  of steps taken per day. are they different, what is the impact
## Finding the total number of steps each day after missing values are imputed and making histogram


```r
   totalStepsByDay1 <- aggregate(steps~date, activity1, sum)
   hist(totalStepsByDay1$steps, xlab = "Class of Total Number of Steps per day", ylab = "Number of Days", main = "Number of Steps taken each day after missing values are imputed")
```

![](PA1_template_files/figure-html/dailysteps-1.png)<!-- -->

## To calculate the mean and median total number of steps per day, first find the total number of steps per day.


```r
totalStepsByDay1 <- aggregate(steps~date, activity1, sum)
```

```r
mean_afterImput <- mean(totalStepsByDay1$steps)
mean_afterImput
```

```
## [1] 10766.19
```
## Mean number of steps taken per day is 1.076618910^{4}

```r
median_afterImput <- median(totalStepsByDay1$steps)
median_afterImput
```

```
## [1] 10766.19
```
## Median number of steps taken per day is 1.076618910^{4}
## By imputting the missing values by the mean number of steps per interval, there is no differnce in mean before and after imputing that is surprising. The median has changed a little bit.

## ** Are there differnces in activity  patterns between weekdays and weekends?**
## Using the weekdays() and the dataset  with the filled  missing values.
## 1. Create a new factor variable in the dataset with two levels - 'Weekdays" and "Weekends"  indicating whether a given date is a weekend or weekday.


```r
 activity1$date <- as.Date(activity1$date)
 library(dplyr)
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:stats':
## 
##     filter, lag
```

```
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
 activity2 <- activity1%>%
  mutate(dayType= ifelse(weekdays(activity1$date) == "Saturday" | weekdays(activity1$date) == "Sunday", "Weekend", "Weekday"))
head(activity2)
```

```
##     steps       date interval dayType
## 1 37.3826 2012-10-01        0 Weekday
## 2 37.3826 2012-10-01        5 Weekday
## 3 37.3826 2012-10-01       10 Weekday
## 4 37.3826 2012-10-01       15 Weekday
## 5 37.3826 2012-10-01       20 Weekday
## 6 37.3826 2012-10-01       25 Weekday
```
## 2. Make panel plot  containing a time series  plot ie type = "l" of the 5-minutes interval and the average number of steps taken, averaged across all weekdays days and weekends days


```r
 averageStepByDayTypeAndInterval <- activity2 %>% group_by(dayType, interval) %>%
 summarize(averageStepByDay=sum(steps))
```

```
## `summarise()` has grouped output by 'dayType'. You can override using the `.groups` argument.
```

```r
 head(averageStepByDayTypeAndInterval)
```

```
## # A tibble: 6 x 3
## # Groups:   dayType [1]
##   dayType interval averageStepByDay
##   <chr>      <int>            <dbl>
## 1 Weekday        0             315.
## 2 Weekday        5             242.
## 3 Weekday       10             231.
## 4 Weekday       15             232.
## 5 Weekday       20             228.
## 6 Weekday       25             283.
```

```r
 library(lattice)
 with(averageStepByDayTypeAndInterval, xyplot(averageStepByDay ~ interval | dayType, type = "l", 
 main = "Total Number of Steps within Intervals by dayType", xlab = "Daily Intervals", ylab = "Average Number of Steps"))
```

![](PA1_template_files/figure-html/lat-1.png)<!-- -->



