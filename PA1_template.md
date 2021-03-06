---
title: "Reproducible Research Project 1"
author: "Angelica Fonseca"
date: "23/1/2021"
output: html_document
---


## What is mean total number of steps taken per day?

```{r Mean steps per day, echo = TRUE}

#Library needed
library(ggplot2)

#Daset URL
dataurl <- 'https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip'

#Download Data
if(!file.exists("activity.csv")){
        download.file(dataurl, destfile = 'repdata%2Fdata%2Factivity.zip', 
                      mode = "wb")
        unzip(zipfile = 'repdata%2Fdata%2Factivity.zip' )
}

#Read Data
outcomes <- read.csv("activity.csv", header = TRUE, sep = ",", na.strings = "NA" )

#First data of dataset
head(outcomes)
#last data of dataset
tail(outcomes)

#Histogram
Mean_steps_per_day <- aggregate(steps~date, outcomes, sum, na.rm = FALSE)
hist(Mean_steps_per_day$steps, main = 'Frequency of steps per day', 
     xlab = 'Steps', col = "lightsteelblue", ylim = c(0,15), 
     breaks =seq(0,25000, by=1000))

#Mean
mean(Mean_steps_per_day$steps)

#Median
median(Mean_steps_per_day$steps)
```
## What is the average daily activity pattern?

```{r Average daily activity pattern, echo = TRUE}
Average_steps <- aggregate(steps~interval, outcomes, mean, na.rm = FALSE)
plot(Average_steps$interval, Average_steps$steps, type = 'l', 
     main = 'Daily activity', xlab = 'Interval (5 min each one)', 
     ylab = 'Average of steps', col = "lightsteelblue", lwd = 3 )

#Mean
mean(Average_steps$steps)

#Median
median(Average_steps$steps)

#Interval that, on average, contains maximum number of steps
Average_steps$interval[which.max((Average_steps$steps))]

```

## Imputing missing values

```{r Missing values, echo = TRUE}

#Total number of NA values in the dataset
sum(is.na(outcomes$steps))

# Mean of NA values in the dataset
Percent_of_NA <- (mean(is.na(outcomes$steps)))*100
Percent_of_NA #The percent of NA values is high

# Imputing missing values with the mean of each interval

#Create a dataset with the steps mean for each interval
outcomes2 <- aggregate(steps~interval,outcomes,mean)

#Set NA values like de mean value for each interval
outcomes$steps[is.na(outcomes$steps)==TRUE] <-  outcomes2$steps #mean(outcomes$steps, na.rm=TRUE)

#Create a new dataset 
Mean_steps_per_day2 <- aggregate(steps~date,outcomes,sum)

#Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

#Historam
hist(Mean_steps_per_day2$steps, main = 'Frequency of steps per day', 
     xlab = 'Steps', col = "lightsteelblue",  ylim = c(0,20), breaks = seq(0,25000, by=1000))

#Mean
mean(Mean_steps_per_day2$steps)

#Median
median(Mean_steps_per_day2$steps)
```

## Are there differences in activity patterns between weekdays and weekends?

```{r Difference of activity between weekdays and weekends, echo = TRUE}

#function for diffenrenciate between weekdays and weekend
wk_function <- function(wk) {
        day_date <- weekdays(as.Date(wk, '%Y-%m-%d'))
        if  ((day_date == 'sábado' || day_date == 'domingo')) {
                x <- 'Weekend'
        } 
        else {
                x <- 'Weekday'
        }
        x
}

#Add a new column to the dataframe
outcomes$day <- as.factor(sapply(outcomes$date, wk_function))

#Aggregate mean of steps by Interval and Day
steps_by_wk <- aggregate(steps~interval+day,outcomes,mean)

#Panel plot comparing the average number of steps taken per 5-minute interval across weekdays and weekends

ggplot(steps_by_wk, aes(interval,steps)) +
        geom_line(stat = 'identity') + 
        facet_wrap(.~day, ncol = 1, nrow = 2)  + 
        labs(x= 'Interval (5 min each one)', y = 'Average of steps') + 
        ggtitle("Diffenrence of steps between weekdays and weekend")

```

```{r knitr}
knit2html()
```

