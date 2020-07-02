Loading and preprocessing the data
----------------------------------

    setwd("~/Coursera class/C5/project1")
    act <- read.csv('./activity.csv')
    head(act)

##### Libraries needed

    library(ggplot2)
    library(mice)

    ## Warning: package 'mice' was built under R version 4.0.2

    ## 
    ## Attaching package: 'mice'

    ## The following objects are masked from 'package:base':
    ## 
    ##     cbind, rbind

What is mean total number of steps taken per day?
-------------------------------------------------

##### aggregate to date level and then create the histogram

    day_steps <- tapply(act$steps, act$date, sum, na.rm=TRUE)
    ##day_steps <- aggregate(steps ~ date, data=act, FUN =sum, na.rm=TRUE)
    hist(day_steps, breaks=20, main="Histogram of total number of steps taken each day")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-3-1.png)

The mean steps taken each day is **9354.23**.

The median steps taken each day is **10395**.

What is the average daily activity pattern?
-------------------------------------------

1.  Make a time series plot of the 5-minute interval (x-axis) and the
    average number of steps taken, averaged across all days (y-axis)

<!-- -->

    steps_by_int <- aggregate(steps ~ interval, data=act, FUN = mean, na.rm=TRUE)
    plot(steps_by_int, type="l", xlab="interval", ylab="avg steps", main = "Activities at each interval across all days")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-4-1.png)

1.  Which 5-minute interval, on average across all the days in the
    dataset, contains the maximum number of steps?

<!-- -->

    steps_by_int$interval[which.max(steps_by_int$steps)]

    ## [1] 835

Imputing missing values
-----------------------

\#\#\#\#\#Use the mice package to impute missing using ‘mean’ method

    md.pattern(act)

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-6-1.png)

    act2 <- mice(act, m=5, maxit=50, meth='mean', seed=500)

    ## Warning: Number of logged events: 1

    completeact <- complete(act2,1)

Histogram of the total number of steps taken each day after missing
values are imputed

    day_steps2 <- tapply(completeact$steps, completeact$date, sum, na.rm=TRUE)
    hist(day_steps2, breaks=20, main="Histogram of total number of steps taken each day after imputing missing")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-7-1.png)

The mean steps taken each day after imputing missing is **10766.19**.

The median steps taken each day after imputing missing is **10766.19**.

Are there differences in activity patterns between weekdays and weekends?
-------------------------------------------------------------------------

Panel plot comparing the average number of steps taken per 5-minute
interval across weekdays and weekends

    completeact$daytype <- ifelse(as.POSIXlt(completeact$date)$wday %in% c(0,6), 'weekend', 'weekday')
    table(completeact$daytype)
    head(completeact)

    steps_by_int2 <- aggregate(steps ~ interval + daytype, data=completeact, FUN = mean, na.rm=TRUE)
    g <- ggplot(steps_by_int2, aes(interval, steps))
    g + geom_line() + facet_grid(daytype ~ .) + xlab("5- minte interval") + ylab("avg number of steps")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-9-1.png)

Yes, there is a difference in activity patterns between weekdays and
weekends!
