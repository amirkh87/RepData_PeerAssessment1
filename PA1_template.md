------------------------------------------------------------------------

output: md\_document: variant: markdown\_github --- \#My 1st Knit Project

Loading and preprocessing the data
----------------------------------

This is the code for preprocessing the data

``` r
setwd("/Users/AmirKh/Documents/R/Reproducible.Research/project1")
activity <- unzip("repdata-data-activity.zip")
act<-read.csv(activity)
rm(activity)
act<- as.data.frame(act)
```

What is mean total number of steps taken per day?
-------------------------------------------------

First, We calculate the total number of steps taken per day :

``` r
act.sum <- aggregate(x =act[c("steps")],
                     FUN = sum,
                     by = list(Group.date = act$date))
act.sum<-act.sum[is.na(act.sum$steps)==FALSE,]
```

histogram of the total number of steps taken each day :

``` r
hist(act.sum$steps,xlab="Total number of steps taken each day",main="Histogram of the total steps taken each day")
```

![](PA1_template_files/figure-markdown_github/unnamed-chunk-4-1.png)

``` r
steps_mean<-mean(act.sum$steps)
steps_median<-median(act.sum$steps)
```

the mean of the total number of steps taken per day is 10766.1886792. the median of the total number of steps taken per day is 10765.

What is the average daily activity pattern?
-------------------------------------------

this is plot of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

``` r
act_avg<-aggregate(steps ~ interval, act, mean)
with(act_avg, plot(interval,steps,type='l'))
```

![](PA1_template_files/figure-markdown_github/unnamed-chunk-6-1.png)

``` r
max_num<- act_avg$interval[which(act_avg$steps==max(act_avg$steps))]
```

the 5-minute interval, on average across all the days in the dataset, that contains the maximum number of steps is 835

Imputing missing values
-----------------------

``` r
NA.sum<-sum(is.na(act))
```

the total number of missing values in the dataset is 2304

Imputing mean steps by interval code :

``` r
act_full<-merge(act,act_avg,all.x=TRUE,by="interval")

for (i in 1:dim(act_full)[1]) {
        if ( is.na(act_full$steps.x[i]) )  {
                
                act_full$steps.x[i]<-act_full$steps.y[i]
        }
}
names(act_full)[2]<-"steps"
act_full<-act_full[,-4]             


act_full.sum <- aggregate(x =act_full[c("steps")],
                     FUN = sum,
                     by = list(Group.date = act_full$date))
act_full.sum<-act_full.sum[is.na(act_full.sum$steps)==FALSE,]
```

here is a histogram of the total number of steps taken each day

``` r
hist(act_full.sum$steps,xlab="Total number of steps taken each day",main="Histogram of the total steps taken each day")        
```

![](PA1_template_files/figure-markdown_github/unnamed-chunk-9-1.png)

``` r
mean_full<-mean(act_full.sum$steps)
median_full<-median(act_full.sum$steps)
```

after the imputing : the mean of total number of steps taken per day is 10766.1886792 the median of total number of steps taken per day is 10766.1886792

Are there differences in activity patterns between weekdays and weekends?
-------------------------------------------------------------------------

the plot below shows the differences in activity patterns between weekdays and weekends

``` r
library(ggplot2) 
act_full$date<-as.Date(act_full$date)
act_full$wk_end<-as.factor(weekdays(act_full$date) %in% c("Saturday","Sunday"))
act_full$wk_end<-factor(act_full$wk_end,labels=c("Weekday","Weekend"))

act_full_avg<-aggregate(steps ~ interval+wk_end, act_full, mean)
k <- ggplot(act_full_avg, aes(interval, steps)) +geom_line()
k + facet_grid(. ~ wk_end)
```

![](PA1_template_files/figure-markdown_github/unnamed-chunk-10-1.png)
