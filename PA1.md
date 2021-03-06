# Reproducible Research: Assignment 1


```r
library(ggplot2)
```
Loading and preprocessing the data

```r
# Read the data
activitydf <- read.csv("data/activity.csv")

#Total number of steps taken per day
stepsTaken <- aggregate(activitydf$steps,
                        list(as.Date(as.character(activitydf$date))),
                        sum)

names(stepsTaken) <- c("Date","Steps")

#Draw the histogram for number of steps taken today
qplot(Steps,data = stepsTaken,geom = "histogram",na.rm = TRUE, binwidth =1000,
      main = "Distribution of Number of steps taken per day", 
      xlab = "Steps per day", ylab = "Count", fill = I("magenta"))
```

![](PA1_files/figure-html/unnamed-chunk-2-1.png)

What is mean total number of steps taken per day?


```r
#Mean and median of total steps per day
meanSteps <- mean(stepsTaken$Steps, na.rm = TRUE)
cat("Mean number of steps per day is",meanSteps)
```

```
## Mean number of steps per day is 10766.19
```

```r
medianSteps <- median(stepsTaken$Steps, na.rm = TRUE)
cat("Median number for steps per day is",medianSteps)
```

```
## Median number for steps per day is 10765
```

What is the average daily activity pattern?


```r
#Average steps for time interval
averageSteps <- aggregate(activitydf$steps,
                          list(activitydf$interval ),
                          mean, na.rm = TRUE)
names(averageSteps) <- c("Interval","Steps")

#Draw time series graph
qplot(Interval,Steps,data = averageSteps, geom = "line", color = "red",
      main = "Steps taken during specific intervals",
      xlab = "Interval", ylab = "Average Steps per day")
```

![](PA1_files/figure-html/unnamed-chunk-4-1.png)

```r
#5 minute interval that has the most steps
stepint <- formatC(averageSteps$Interval[which.max(averageSteps$Steps)], 
        width = 4, flag = 0)
cat("Five minute interval which has the most steps is",stepint)
```

```
## Five minute interval which has the most steps is 0835
```

```r
#Total number of rows which has NA dataset
cat("No of missing values in dataset is",
    length(which(is.na(activitydf$steps))))
```

```
## No of missing values in dataset is 2304
```

Imputing missing values


```r
#Strategy for filling NAs
#New dataset
newSteps <- activitydf

#Filling missing NAs
na_rows <- which(is.na(activitydf$steps))
avg_int <- setNames(as.list(averageSteps$Steps),averageSteps$Interval)
newSteps$steps[na_rows] <- round(as.numeric(avg_int[as.character(activitydf$interval[na_rows])]),3)

#Draw the histogram for number of steps taken today for new dataset
newStepsTaken <- aggregate(newSteps$steps,
                        list(as.Date(as.character(newSteps$date))),
                        sum)

names(newStepsTaken) <- c("Date","Steps")
qplot(Steps,data = newStepsTaken,geom = "histogram", 
      main = "Distribution of Number of steps taken per day", 
      xlab = "Steps per day", ylab = "Count", fill = I("blue"),
      na.rm = TRUE, binwidth =1000)
```

![](PA1_files/figure-html/unnamed-chunk-5-1.png)

```r
#Mean and median of total steps per day for new dataset
meanNewSteps <- mean(newStepsTaken$Steps, na.rm = TRUE)
cat("Mean number of steps per day is",meanNewSteps)
```

```
## Mean number of steps per day is 10766.19
```

```r
medianNewSteps <- median(newStepsTaken$Steps, na.rm = TRUE)
cat("Median number for steps per day is",medianNewSteps)
```

```
## Median number for steps per day is 10766.19
```

 Are there differences in activity patterns between weekdays and weekends?
 

```r
#Weekdays and Weekend differences
newSteps <- cbind(newSteps[,1:3],ifelse(weekdays(
            as.Date(as.character(newSteps$date))) %in% 
            c("Saturday","Sunday"),"Weekend","Weekday" ))

names(newSteps)[4] <- "WDay"


averageWdaySteps <- aggregate(newSteps$steps,
                          list(newSteps$interval,newSteps$WDay ),
                          mean, na.rm = TRUE)
names(averageWdaySteps) <- c("Interval","Wday","Steps")


qplot(Interval,Steps,data = averageWdaySteps, facets = .~Wday, 
      geom = "line", color = Wday,
      main = "Steps taken during specific intervals",
      xlab = "Interval", ylab = "Average Steps per day")
```

![](PA1_files/figure-html/unnamed-chunk-6-1.png)
