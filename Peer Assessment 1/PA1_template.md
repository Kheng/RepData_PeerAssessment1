# Reproducible Research: Peer Assessment 1

### Introduction

It is now possible to collect a large amount of data about personal movement using 
activity monitoring devices such as a Fitbit, Nike Fuelband, or Jawbone Up. These 
type of devices are part of the “quantified self” movement – a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. But these data remain under-utilized both because the raw data are hard to obtain and there is a lack of statistical methods and software for processing and interpreting the data.

This makes use of data from a personal activity monitoring device. This device collects data at 5 minute intervals through out the day. The data consists of two months of data from an anonymous individual collected during the months of October and November, 2012 and include the number of steps taken in 5 minute intervals each day.

###Objective
* Analize the data from Fitbit utilizing the skills learned from this course, as well as those that were learned from the previous courses of the Data Science track.
* Use R markdown so that individuals who might be interested can replicate this work.

  

###Data

The data for this assignment can be downloaded from the course web site:

* Dataset: [Activity monitoring data](https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip) [52K]

The variables included in this dataset are:

* **steps:** Number of steps taking in a 5-minute interval (missing values are coded as `NA`)

* **date:** The date on which the measurement was taken in YYYY-MM-DD format

* **interval:** Identifier for the 5-minute interval in which measurement was taken

The dataset is stored in a comma-separated-value (CSV) file and there are a total of 17,568 observations in this dataset.

### Loading and preprocessing the data


```r
setwd("/Users/Kheng/Documents/Data Sceince/Reproducible Research/Peer Assessment 1")
data <- read.csv("activity.csv", header = TRUE)
data.clean <- na.omit(data)
```

### What is the mean total number of steps taken per day?

**1. Histogram**

```r
agr <- aggregate(steps~ date, data = data.clean,sum)
#pdf("histogram_of_steps_per_day.pdf")
hist(agr$steps, xlab = "steps per day", main = "histogram of total number of steps taken per day")
```

![](PA1_template_files/figure-html/unnamed-chunk-2-1.png) 

```r
#dev.off()
```
**2.
Mean number of steps per day**


```r
steps.mean <- mean(agr$steps)
steps.mean
```

```
## [1] 10766.19
```
**Median number of steps per day**


```r
steps.median <- median(agr$steps)
steps.median
```

```
## [1] 10765
```

### What is the average daily activity pattern?


**1. Time series plot of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)**


```r
steps.int <- aggregate(steps~interval, data = data.clean, mean)
plot(steps.int, type = "l")
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png) 

**2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?**

```r
steps.int[which.max(steps.int$steps),1]
```

```
## [1] 835
```

###Imputing missing values

** 1. The number of missing values in the dataset is**

```r
length(which(is.na(data)))
```

```
## [1] 2304
```

** 2.&3. Use the mean for a give interval to impute missing values and assign to data set named 'data.c'**

```r
rownames(steps.int) <- steps.int$interval
data.c <- data
for(i in 1:dim(data.c)[1]){
  data.c[i,1] <- ifelse(is.na(data.c[i,1]),steps.int[as.character(data.c[i,3]),2],data.c[i,1])
	#print(steps.int[as.character(data.c[i,3]),1])	
}
```
**4. Make a histogram of the total number of steps taken each day on the imputed dataset**

```r
agr.imp <- aggregate(steps~ date, data = data.c,sum)
hist(agr.imp$steps, xlab = "steps per day", main = " total number of steps taken each day (imputed data)")
```

![](PA1_template_files/figure-html/unnamed-chunk-9-1.png) 

**Mean number of steps per day (imputed)**


```r
steps.mean.imp <- mean(agr.imp$steps)
steps.mean.imp
```

```
## [1] 10766.19
```
**Median number of steps per day (imputed)**


```r
steps.median.imp <- median(agr.imp$steps)
steps.median.imp
```

```
## [1] 10766.19
```
**In this case imputation of the values did not affect the estimates of the total daily number of steps taken**

### Are there differences in activity patterns between weekdays and weekends?

** 1. Create new factor variable - weekday/weekend**

```r
data.c$date <- as.Date(as.character(agr.imp$date))
data.c$day <- weekdays(data.c$date)
data.c$w <- ifelse(data.c$day=="Saturday" | data.c$day=="Sunday","weekend","weekday")
data.c$w <- as.factor(data.c$w)
```
** 2. Create a panel time series plot** 

```r
agr.imp.weekday <- aggregate(steps ~ interval, data = data.c[data.c$w=="weekday",],mean)
agr.imp.weekend <- aggregate(steps ~ interval, data = data.c[data.c$w=="weekend",],mean)

par(mfrow = c(2,1))
plot(agr.imp.weekday, type = "l", xlab = "intervals",ylab = "average steps/interval",main = "weekdays")
plot(agr.imp.weekend, type = "l",xlab = "intervals",ylab = "average steps/interval", main = "weekends")
```

![](PA1_template_files/figure-html/unnamed-chunk-13-1.png) 
