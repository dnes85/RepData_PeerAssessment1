# Reproducible Research: Peer Assessment 1
Diego Esteves  
Sunday, April 17, 2016  

## Loading Libraries 

```r
library("ggplot2") 
library("dplyr") 
library('knitr')

# note: install packages before 
# -> install.packages('ggplot2', dep = TRUE)
# -> install.packages('dplyr', dep = TRUE)
```

## Loading and preprocessing the data

```r
unzip("activity.zip")
activity <- read.csv("activity.csv", header = TRUE, sep = ",", colClasses = c("numeric","character","numeric"))
```

## What is mean total number of steps taken per day?

#### 1. Make a histogram of the total number of steps taken each day


```r
activityPerDay <- aggregate(steps ~ date, activity, sum)

#create histogram plot
ggplot(activityPerDay, aes(steps)) + 
  geom_histogram(color = "blue", fill = "white") + 
  ggtitle("Distribution of total steps per day")
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png)

#### 2. Calculate and report the mean and median total number of steps taken per day


```r
smean <- mean(activityPerDay$steps)
smedian <- median(activityPerDay$steps)

smean
```

```
## [1] 10766.19
```

```r
smedian
```

```
## [1] 10765
```

## What is the average daily activity pattern?

#### 1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
steps.interval <- aggregate(steps ~ interval, data=activity, FUN=mean)
plot(steps.interval, type="l")
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png)

#### 2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
steps.interval$interval[which.max(steps.interval$steps)]
```

```
## [1] 835
```

## Imputing missing values

#### 1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
sum(is.na(activity))
```

```
## [1] 2304
```


#### 2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

#### 3. Create a new dataset that is equal to the original dataset but with the missing data filled in.


```r
activityFilled <- merge(activity, steps.interval, by="interval", suffixes=c("",".y"))
nas <- is.na(activityFilled$steps)
activityFilled$steps[nas] <- activityFilled$steps.y[nas]
activityFilled <- activityFilled[,c(1:3)]
```

#### 4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


```r
steps.date <- aggregate(steps ~ date, data=activityFilled, FUN=sum)
barplot(steps.date$steps, names.arg=steps.date$date, xlab="date", ylab="steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-9-1.png)

```r
mean(steps.date$steps)
```

```
## [1] 10766.19
```

```r
median(steps.date$steps)
```

```
## [1] 10766.19
```

## Are there differences in activity patterns between weekdays and weekends?

#### 1. Create a new factor variable in the dataset with two levels ??? ???weekday??? and ???weekend??? indicating whether a given date is a weekday or weekend day.


```r
activity$day=ifelse(as.POSIXlt(as.Date(activity$date))$wday%%6==0,
                          "weekend","weekday")
# For Sunday and Saturday : weekend, Other days : weekday 
activity$day=factor(activity$day,levels=c("weekday","weekend"))
```


#### 2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). The plot should look something like the following, which was creating using simulated data:


```r
stepsInterval2=aggregate(steps~interval+day,activity,mean)
library(lattice)
xyplot(steps~interval|factor(day),data=stepsInterval2,aspect=1/2,type="l")
```

![](PA1_template_files/figure-html/unnamed-chunk-11-1.png)
