# Reproducible Research Assignment 1

### Load data


```r
activity <- read.csv("activity.csv", header=T,stringsAsFactors=F, colClasses=c('numeric', 'Date', 'numeric'), na.strings="NA")
```

### Total steps taken each day.


```r
library(data.table)
activity <- data.table(activity)
stepsPerDay <- activity[, lapply(.SD, sum, na.rm=TRUE), by=date, .SDcols=c('steps')]
hist(stepsPerDay$steps, main=" Histogram of the total number of steps taken each day", xlab="Steps per Day")
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2.png) 
### Averge and Median steps per day

```r
mean <- mean(stepsPerDay$steps, na.rm=TRUE)
mean
```

```
## [1] 9354
```

```r
median <- median(stepsPerDay$steps, na.rm=TRUE)
median
```

```
## [1] 10395
```
### Daily Average activity pattern.


```r
DailyAvg_Pattern <- activity[, lapply(.SD, mean, na.rm=TRUE), by=interval, .SDcols=c('steps')]
plot(DailyAvg_Pattern , type="l", xlab="Daily Average", ylab="Number of Steps")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4.png) 
### Maximum number of steps per 5 minute interval.

```r
DailyAvg_Pattern[which.max(DailyAvg_Pattern$steps)]$interval
```

```
## [1] 835
```
### Imputing missing values.

```r
NA_Values <- sum(is.na(activity$steps))
NA_Values
```

```
## [1] 2304
```
### Cleansing data and merging new data set.

```r
Avg_stepsPerDay <- activity[, lapply(.SD, mean, na.rm=TRUE), by=date, .SDcols=c('steps')]
New_Data <- merge(activity, Avg_stepsPerDay, by="date")
New_Data$steps <- ifelse(is.na(New_Data$steps.x), New_Data$steps.y, New_Data$steps.x)
```
### Cleansed Total of steps taken each day.

```r
hist(New_Data$steps, main=" Cleansed Histogram of Steps per Day ", xlab="Steps per Day")
```

![plot of chunk unnamed-chunk-8](figure/unnamed-chunk-8.png) 
### New mean and median values

```r
new_mean <- mean(New_Data$steps, na.rm=TRUE)
new_mean
```

```
## [1] 37.38
```

```r
new_median <- median(New_Data$steps, na.rm=TRUE)
new_median
```

```
## [1] 0
```
### Activity Differences by Weekdays and Weekends

```r
New_Data$dayType <- "weekday"
Sys.setlocale(category = "LC_ALL", locale = "English")
```

```
## [1] "LC_COLLATE=English_United States.1252;LC_CTYPE=English_United States.1252;LC_MONETARY=English_United States.1252;LC_NUMERIC=C;LC_TIME=English_United States.1252"
```

```r
New_Data[weekdays(New_Data$date) == "Saturday" | weekdays(New_Data$date) == "Sunday"]$dayType <- "weekend"
PlotData<- New_Data[, lapply(.SD, mean, na.rm=TRUE), by=c("dayType", "interval"), .SDcols=c('steps')]
par(mfrow=c(2,1))
with(PlotData[PlotData$dayType == 'weekday'], plot(interval, steps, type="l", main="Weekday", xlab="Interval", ylab="Number of Steps"))
with(PlotData[PlotData$dayType == 'weekend'], plot(interval, steps, type="l", main="Weekend", xlab="Interval", ylab="Number of Steps"))
```

![plot of chunk unnamed-chunk-10](figure/unnamed-chunk-10.png) 


