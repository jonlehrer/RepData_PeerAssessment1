# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data

```r
activity <- read.csv("activity.csv")
activity$date <- as.Date(activity$date)
```


## What is mean total number of steps taken per day?
First, we sum by day.  
Then we take a mean of those daily values.  

```r
ag <- aggregate(steps ~ date, data=activity, FUN=sum)
ms <- mean(ag$steps)
```
**The mean total number of steps taken per day is 1.0766 &times; 10<sup>4</sup>**


## What is the average daily activity pattern?
For the sake of simplicity, we have kept the time interval in its original scale.

```r
ag_5min <- aggregate(steps ~ interval, FUN=mean, data=activity)
library(ggplot2)
qplot(interval, steps, data=ag_5min, geom="line")
```

![plot of chunk avgdaily](./PA1_template_files/figure-html/avgdaily.png) 



## Imputing missing values
We use the mean steps across all days for the missing interval in question

```r
act_imp <- activity
nas <- is.na(act_imp$steps)
act_imp$steps[nas] <- ag_5min[match(act_imp$interval[nas], ag_5min$interval),"steps"]
```


## Are there differences in activity patterns between weekdays and weekends?
First, two separate plots:

```r
act_imp$weekend <- weekdays(act_imp$date) %in% c("Saturday", "Sunday")
act_imp$weekend <- as.factor(act_imp$weekend)
levels(act_imp$weekend) <- c("Weekday", "Weekend")
qplot(interval, steps, data=act_imp, facets=weekend~., geom="line")
```

![plot of chunk weekend1](./PA1_template_files/figure-html/weekend1.png) 
  
And overlayed on the same set of axes:

```r
g <- ggplot(act_imp, aes(interval, steps))
g + geom_line(aes(color=weekend), size=2, alpha=.5)
```

![plot of chunk weekend2](./PA1_template_files/figure-html/weekend2.png) 

**There appears to be a noticeable difference between weekend and weekday activity.**
