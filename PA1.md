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

Since we are looking at the NA's in the data, I am going to switch back to the original data set that was loaded. Here, I am going to check if he interval and date columns have any missing values or not:


```r
length(data$date[is.na(data$date) == TRUE])
```

```
## [1] 0
```

```r
length(data$interval[is.na(data$interval) == TRUE])
```

```
## [1] 0
```


Since neither of them have missing values, then that leaves us with the steps column. Thus, the total number of rows in the data set with NA's is the same as the total number of NA's in the steps column, which can be calculated by the following:


```r
length(data$steps[is.na(data$steps) == TRUE])
```

```
## [1] 2304
```


This tells us there are 2304 rows with missing values in the data.

To keep things simple, I am just going to replace the missing values using the mean number of steps for the same day. First, I'll create that temporary data frame (without NA's):


```r
mdata <- aggregate(smdata$steps, list(day = smdata$date), mean)
```


Then I will create a new data set that is a copy of the original data set:


```r
fulldata <- data
head(fulldata)
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


Now I am going to fill in the missing data with the day's mean:


```r
# for each row in fulldata
for (i in 1:length(fulldata$steps)) {
    # check if the row has NA in its steps
    if (is.na(fulldata$steps[i])) {
        # convert the date into a Date object
        mydate <- as.Date(fulldata$date[i])
        
        # see if that date has a mean in mdata
        rez <- mydate == as.Date(mdata$day)
        
        # by default, if mydate does not have a mean, then no steps data exist for
        # that date, so just assign the average to be 0
        myavg <- 0
        
        # otherwise we found the date in mdata, so get that date's mean stored in
        # column x
        if (length(which(rez == TRUE)) > 0) {
            index <- which(rez == TRUE)
            myavg <- mdata$x[index]
        }
        
        # replace NA
        fulldata$steps[i] <- myavg
    }
    # otherwise, the row does not have missing values
}
```


Let's ensure there are 0 NA's left:


```r
length(fulldata$steps[is.na(fulldata$steps) == TRUE])
```

```
## [1] 0
```


Repeating the code from earlier, here is the histogram, the mean, and median of the total number of steps each day found using the imputed data:

```r
s2data <- aggregate(fulldata$steps, list(day = fulldata$date), sum)
hist(s2data$x, breaks = 20, main = "Histogram of Total Number of Days (using Imputed Data)", 
    xlab = "Total Number of Steps (Summed over Intervals)")
```

![plot of chunk unnamed-chunk-15](figure/unnamed-chunk-15.png) 

```r
mean(s2data$x)
```

```
## [1] 9354
```

```r
median(s2data$x)
```

```
## [1] 10395
```


Because my imputation strategy replaces missing values with 0 when no mean exists for that date, we see that the histogram displayed this time has a lot more counts for 0. For this reason, the mean is lower than before (it used to be 10766), and the median also got pulled to a smaller number than before (it used to be 10765).

However, there is no impact on the rest of the data when we are looking at the total daily number of steps.

## Are there differences in activity patterns between weekdays and weekends?

I will first modify fulldata with an additional factor that denotes whether the date in that row is a weekday or a weekend day. Just to be sure, I will check that the data has both weekdays and weekends in them:


```r
# modify fulldata with additional factor
fulldata$day <- weekdays(as.Date(fulldata$date))
selected = cbind("Saturday", "Sunday")
fulldata$wday <- ifelse(fulldata$day %in% selected, "weekend", "weekday")
head(fulldata)
```

```
##   steps       date interval    day    wday
## 1     0 2012-10-01        0 Monday weekday
## 2     0 2012-10-01        5 Monday weekday
## 3     0 2012-10-01       10 Monday weekday
## 4     0 2012-10-01       15 Monday weekday
## 5     0 2012-10-01       20 Monday weekday
## 6     0 2012-10-01       25 Monday weekday
```

```r
unique(fulldata$day)
```

```
## [1] "Monday"    "Tuesday"   "Wednesday" "Thursday"  "Friday"    "Saturday" 
## [7] "Sunday"
```

```r
unique(fulldata$wday)
```

```
## [1] "weekday" "weekend"
```


Now I will split the data up into a subset that only has weekdays in it and another subset that only has weekends in it, and put them into the time series format as earlier:



```r
d1 <- fulldata[fulldata$wday == "weekday", ]
i1data <- aggregate(d1$steps, list(interval = d1$interval), mean)
d2 <- fulldata[fulldata$wday == "weekend", ]
i2data <- aggregate(d2$steps, list(interval = d2$interval), mean)
```


Now I will do a two-panel plot on the time series data:


```r
par(mfrow = c(2, 1))
plot(i1data, type = "l", main = "Weekdays", xlab = "Interval", ylab = "Number of Steps (Averaged over Days)")
plot(i2data, type = "l", main = "Weekends", xlab = "Interval", ylab = "Number of Steps (Averaged over Days)")
```

![plot of chunk unnamed-chunk-18](figure/unnamed-chunk-18.png) 

