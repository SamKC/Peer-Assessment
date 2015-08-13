# Peer-Assessment
Reproducible Research: course projects/assignments
---

## Introduction:
This project demonstrates how to write a report in a sigle R markdown document through answering the questions of assignments. Data and questions can be downloaded from the ["Reproducible Research" course webpage](https://www.coursera.org/course/repdata). Data is collecting from a personal activity monitoring device at 5-minute intervals through out the day and consists of two months of data  collected during the months of October and November, 2012.

To make a start on the assignment, Rstudio is implemented.

#### Prepare the R environment
Setting the global option to show the code with echo = TRUE and makes each time of running in html  faster when setting cache equals TRUE.
```{r}
library(knitr)
opts_chunk$set(echo = TRUE,results = "hold", cache = TRUE)
```
Loading the data from the "activity.csv" file
```{r}
activity <- read.csv("activity.csv", colClasses = c("numeric", "Date", "numeric"))
```
To understand the data briefly, the following codes are used:

```{r}
head(activity)
names(activity)
str(activity)
summary(activity)
```
#### What is mean total number of steps taken per day?
To calculate the total number of steps taken per day, the aggregate function is called and NA is also removed from the data.
```{r}
Total_Steps <- aggregate(steps ~ date, data = activity, sum, na.rm = TRUE)
```
The following is the last 6 numbers tail of the total steps taken per day:
```{r}
Total_StepsTail <- tail(Total_Steps)
Total_StepsTail$steps
```

To show histogram of total numbers of steps taken per day:
```{r}
hist(Total_Steps$steps, xlab = " ", main = "Total Numbers of Steps Taken per Day", col= "blue",breaks = 10)
```

To calculate its mean and median:
```{r}
itsMean <- mean(Total_Steps$steps)
itsMean 
itsMedian <- median(Total_Steps$steps)
itsMedian
```

#### What is the average daily activity pattern?

Make a time series plot
```{r}
stepsInterval <- aggregate(steps ~ interval, data = activity, mean, na.rm = TRUE)
plot(stepsInterval$interval,stepsInterval$steps, type="l", xlab="5-minute  Interval", ylab="Steps Averaged across all Days",main="Average Number of Steps averaged across all days", col = " red")
```

```{r}
maxInterval <- stepsInterval[which.max(stepsInterval$steps),1]
```
The maximum number of steps averaged across all the days in 5-minute interval is "`r maxInterval`".

#### Imputing missing values

To calculate the total number of missing values:
```{r}
Total_missing <- sum(is.na(activity))
Total_missing
```

To refill the NA,a "for loop"" function is used and replaced with the 5-minute interval mean. Then the replacing NA is assigned to a new data set called activity_NoNa with the same dimension of the original activity data.

```{r}
stepsInterval <- aggregate(steps ~ interval, data = activity, mean)
replaceNA <- numeric()
for (i in 1:nrow(activity)) {
    act <- activity[i, ]
    if (is.na(act$steps)) {
        steps <- subset(stepsInterval, interval == act$interval)$steps
    } else {
        steps <- act$steps
    }
    replaceNA <- c(replaceNA, steps)
}
activity_NoNa <- activity
activity_NoNa$steps <- replaceNA
```

The Histogram with replacing NA is
```{r}
NewTotalSteps <- aggregate(steps ~ date, data = activity_NoNa, sum, na.rm = TRUE)
hist(NewTotalSteps$steps,  xlab = " ", main = paste("Total Numbers of Steps Taken per Day"), col= "red",breaks = 10)
```     

The New mean and median are:
```{r}
New_Mean <- mean(NewTotalSteps$steps)
New_Mean
New_Median <- median(NewTotalSteps$steps)
New_Median
```

Before filling the data

.Mean  :  10766.189  
.Median:  10765

After filling the data

.Mean  :  10766.189  
.Median:  10766.189  

Therefore, the values mean before and after filling NA is equivalent, but the median is slightly different by 1.189.

#### Are there differences in activity patterns between weekdays and weekends?
Create a factor variable in the dataset with two levels weekday and weekend.
```{r}
AllDay <- weekdays(activity_NoNa$date)
Day <- vector()
for (i in 1:nrow(activity)) {
    if (AllDay[i] == "Saturday") {
        Day[i] <- "Weekend"
    } else if (AllDay[i] == "Sunday") {
        Day[i] <- "Weekend"
    } else {
        Day[i] <- "Weekday"
    }
}
activity_NoNa$Day <- Day
activity_NoNa$Day <- factor(activity_NoNa$Day)

steps_Day <- aggregate(steps ~ interval + Day, data = activity_NoNa, mean)
names(steps_Day) <- c("interval", "Day", "steps")
```

The following is the panel plot comparing the average number of steps taken per 5-minute interval between across weekdays and weekends.
```{r}
library(lattice)
xyplot(steps ~ interval | Day, steps_Day, type = "l", layout = c(1, 2), 
    xlab = "The 5-minute Interval", ylab = "Number of steps")
```
To refill the NA,a "for loop"" function is used and replaced with the 5-minute interval mean. Then the replacing NA is assigned to a new data set called activity_NoNa with the same dimension of the original activity data.

```{r}
stepsInterval <- aggregate(steps ~ interval, data = activity, mean)
replaceNA <- numeric()
for (i in 1:nrow(activity)) {
    act <- activity[i, ]
    if (is.na(act$steps)) {
        steps <- subset(stepsInterval, interval == act$interval)$steps
    } else {
        steps <- act$steps
    }
    replaceNA <- c(replaceNA, steps)
}
activity_NoNa <- activity
activity_NoNa$steps <- replaceNA
```

The Histogram with replacing NA is
```{r}
NewTotalSteps <- aggregate(steps ~ date, data = activity_NoNa, sum, na.rm = TRUE)
hist(NewTotalSteps$steps,  xlab = " ", main = paste("Total Numbers of Steps Taken per Day"), col= "red",breaks = 10)
```     

The New mean and median are:
```{r}
New_Mean <- mean(NewTotalSteps$steps)
New_Mean
New_Median <- median(NewTotalSteps$steps)
New_Median
```

Before filling the data

.Mean  :  10766.189  
.Median:  10765

After filling the data

.Mean  :  10766.189  
.Median:  10766.189  

Therefore, the values mean before and after filling NA is equivalent, but the median is slightly different by 1.189.

#### Are there differences in activity patterns between weekdays and weekends?
Create a factor variable in the dataset with two levels weekday and weekend.
```{r}
AllDay <- weekdays(activity_NoNa$date)
Day <- vector()
for (i in 1:nrow(activity)) {
    if (AllDay[i] == "Saturday") {
        Day[i] <- "Weekend"
    } else if (AllDay[i] == "Sunday") {
        Day[i] <- "Weekend"
    } else {
        Day[i] <- "Weekday"
    }
}
activity_NoNa$Day <- Day
activity_NoNa$Day <- factor(activity_NoNa$Day)

steps_Day <- aggregate(steps ~ interval + Day, data = activity_NoNa, mean)
names(steps_Day) <- c("interval", "Day", "steps")
```

The following is the panel plot comparing the average number of steps taken per 5-minute interval between across weekdays and weekends.
```{r}
library(lattice)
xyplot(steps ~ interval | Day, steps_Day, type = "l", layout = c(1, 2), 
    xlab = "The 5-minute Interval", ylab = "Number of steps")
```