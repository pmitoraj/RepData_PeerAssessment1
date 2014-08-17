# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data
Extract the zip archive: 

```r
unzip('activity.zip')
```
Load the data:

```r
data <- read.csv('activity.csv')
```
## What is mean total number of steps taken per day?
Create an array with steps taken on each day:

```r
daily_steps <- with(data, tapply(steps,date,sum))
```
Create a historgam of amount of steps taken each day:

```r
hist(daily_steps)
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4.png) 

Mean value of steps taken each day:

```r
mean(daily_steps,na.rm=TRUE)
```

```
## [1] 10766
```
Median value of steps taken each day:

```r
median(daily_steps,na.rm=TRUE)
```

```
## [1] 10765
```
## What is the average daily activity pattern?

Coerce the time interval into factor:

```r
data$interval <- as.factor(data$interval)
```

Create an array of steps taken in each 5-minute interval averaged across all days:

```r
interval_step <- with(data, tapply(steps,interval,mean,na.rm=TRUE))
```

Time series plot of the average amount of steps taken in each time interval averaqed across all days:

```r
labels <- dimnames(interval_step)[[1]]
plot(labels,interval_step,type='l',xlab="Time interval ID",ylab="Averaged amount of steps")
```

![plot of chunk unnamed-chunk-9](figure/unnamed-chunk-9.png) 

Maximum averaged amount of steps taken in 5-minute interval:

```r
sort(interval_step,decreasing=TRUE)[1]
```

```
##   835 
## 206.2
```


## Imputing missing values
The total number of missing values in the dataset:

```r
sum(is.na(data$steps))
```

```
## [1] 2304
```

The strategy for imputing missing values is to replace them with the mean value for that specific 5-minute interval.
Create the list of indexes of missing values:

```r
na_indexes <- which(is.na(data$steps))
```

Create a data frame with column na_index and time_interval_index. The former indicates the record number with the missing value, 
whereas the latter denotes the 5-minute time interval index.

```r
df <- data.frame(na_indexes, na_indexes %% 288)
names(df) <- c('na_index','time_interval_index')
df$time_interval_index[df$time_interval_index == 0] <- 288
```


```r
imputed_data <- data
# imputed_data will have its missing values replaced with values from interval_step
# using df as a translation table
for(i in 1:nrow(df)) {
  imputed_data$steps[df[i,1]] <- interval_step[df[i,2]]
}
```


Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

Create an array with steps taken on each day:

```r
imputed_daily_steps <- with(imputed_data, tapply(steps,date,sum))
```

Create a historgam of amount of steps taken each day:

```r
hist(imputed_daily_steps)
```

![plot of chunk unnamed-chunk-16](figure/unnamed-chunk-16.png) 
Mean value of steps taken each day:

```r
mean(imputed_daily_steps,na.rm=TRUE)
```

```
## [1] 10766
```
Median value of steps taken each day:

```r
median(imputed_daily_steps,na.rm=TRUE)
```

```
## [1] 10766
```

Imputing the data with the mean for the specific 5-minute interval did not change the mean value of steps taken each day, however the median changed.

The data is missing for all records in specific day. There are no days with partial data. Therefore, imputing data had effect only on the daily amount of steps on the days that had no data. For other days the value did not change since no records were imputed 

