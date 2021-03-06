# Reproducible Research: Peer Assessment 1
This is the R markdown document for the assignment.

## Loading and preprocessing the data
First I'm going to require the library and load the data:

```r
library(knitr)
data <- read.csv("activity.csv")
head(data)
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



## What is mean total number of steps taken per day?

Since we want to exclude missing values, I am going to first operate on only the data without NA's: 


```r
smdata <- na.omit(data)
head(smdata)
```

```
##     steps       date interval
## 289     0 2012-10-02        0
## 290     0 2012-10-02        5
## 291     0 2012-10-02       10
## 292     0 2012-10-02       15
## 293     0 2012-10-02       20
## 294     0 2012-10-02       25
```


Next, I will now aggregate the data by date and look at the total number of steps taken each day:


```r
sdata <- aggregate(smdata$steps, list(day = smdata$date), sum)
head(sdata)
```

```
##          day     x
## 1 2012-10-02   126
## 2 2012-10-03 11352
## 3 2012-10-04 12116
## 4 2012-10-05 13294
## 5 2012-10-06 15420
## 6 2012-10-07 11015
```


Here is the histogram, the mean, and median of the total number of steps each day:

```r
hist(sdata$x, breaks = 20, main = "Histogram of Total Number of Days", xlab = "Total Number of Steps (Summed over Intervals)")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4.png) 

```r
mean(sdata$x)
```

```
## [1] 10766
```

```r
median(sdata$x)
```

```
## [1] 10765
```


## What is the average daily activity pattern?
I am assuming that we are still ignore all the NA data here. So I will continue this part of the assignment with smdata that I created above. This time, I will aggregate by interval and average the steps over days:


```r
idata <- aggregate(smdata$steps, list(interval = smdata$interval), mean)
head(idata)
```

```
##   interval       x
## 1        0 1.71698
## 2        5 0.33962
## 3       10 0.13208
## 4       15 0.15094
## 5       20 0.07547
## 6       25 2.09434
```

```r
tail(idata)
```

```
##     interval      x
## 283     2330 2.6038
## 284     2335 4.6981
## 285     2340 3.3019
## 286     2345 0.6415
## 287     2350 0.2264
## 288     2355 1.0755
```


As indicated in the instructions, I will now plot this data:


```r
plot(idata, type = "l")
title(main = "Average Daily Activity Pattern", xlab = "Interval", ylab = "Number of Steps (Averaged over Days)")
```

![plot of chunk unnamed-chunk-6](figure/unnamed-chunk-6.png) 


The interval with the maximum number of steps from this data is:


```r
which.max(idata$x)
```

```
## [1] 104
```


Which corresponds with the following interval value:


```r
idata$interval[which.max(idata$x)]
```

```
## [1] 835
```


## Imputing missing values



## Are there differences in activity patterns between weekdays and weekends?
