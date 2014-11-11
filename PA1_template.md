# Reproducible Research Peer Assessment One
Bart Browning  
11/10/2014  

##1. Read and Process the dataset:##

```r
steps  <- read.csv(file="../../activity.csv",head=TRUE,sep=",", colClasses=c(NA,"Date",NA))
notNa <- subset(steps, steps!="NA")
notNaStepsPerDay <- aggregate(steps ~ date, data = notNa, FUN = sum)
```

##2. Mean total number of steps taken per day:##

+ Histogram of total number of steps taken per day:


```r
hist(notNaStepsPerDay$steps, main="", xlab="steps per day (NA values removed)")
```

![](./PA1_template_files/figure-html/hist-total-steps-per-day-1.png) 

+ the __mean__ number of steps taken each day is: 10766.19
+ the __median__ number of steps taken each day is: 10765.00

##3. Steps taken (averaged across all days) versus the 5-minute intervals:##

```r
notNaAverageStepsPerInterval <- aggregate(steps ~ interval, data = notNa, FUN = mean)
plot(notNaAverageStepsPerInterval, type="l")  
```

![](./PA1_template_files/figure-html/steps-taken-during-each-interval-1.png) 

```r
maxInterval <- notNaAverageStepsPerInterval[which.max(notNaAverageStepsPerInterval$steps),]$interval
```

+ The 5-minute-interval that on average contains the maximum number of steps is: 835

##4. Imputed-data strategy: replace all NA values in the original data with the average-value for the relevant 5-minute-interval:##

```r
isNa     <- subset(steps, is.na(steps))[,c(2,3)]                                                                                            
imputeNa <- merge(isNa, notNaAverageStepsPerInterval, by.x="interval", by.y="interval")                                               
imputed  <- rbind(imputeNa, notNa)                                                                                                     
```

+ Re-compute the histogram from step #2, but with imputed data added in place of NA:

```r
 imputedStepsPerDay <- aggregate(steps ~ date, data = imputed, FUN = sum)                                                               
 hist(imputedStepsPerDay$steps, main="", xlab="steps per day (NA values replaced)")                                                                                                         
```

![](./PA1_template_files/figure-html/re-compute-histogram-1.png) 

+ the __mean__ number of steps taken each day is now: 10766.19
+ Difference from step #2: 0.00
+ the __median__ number of steps taken each day is now: 10766.19
+ Difference from step #2: 1.19

##5. Average steps taken per 5-minute-interval (weekdays versus weekends):##

```r
library(timeDate)                                                                                                                     
newCol      <- ifelse(isWeekday(imputed$date), "weekday", "weekend")                                                                       
imputedWday <- cbind(imputed, newCol)                                                                                                   
weekday     <- subset(imputedWday, newCol=="weekday")                                                                                     
weekend     <- subset(imputedWday, newCol=="weekend")                                                                                     
weekdayASPI <- aggregate(steps ~ interval, data = weekday, FUN = mean)                                                                
weekendASPI <- aggregate(steps ~ interval, data = weekend, FUN = mean)                                                                
par(mfrow=c(2,1))                                                                                                                     
plot(weekdayASPI, type="l", main="Weekday avg steps per interval")
plot(weekendASPI, type="l", main="Weekend avg steps per interval")
```

![](./PA1_template_files/figure-html/weekday-versus-weekend-1.png) 

