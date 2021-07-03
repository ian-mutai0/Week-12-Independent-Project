---
title: "Week 12 Independent Project"
author: "Mr. Mutai"
date: "7/3/2021"
output:
  html_document: 
    keep_md: yes
  pdf_document: default
---



# ASSESSMENT QUESTION

A Kenyan entrepreneur has created an online cryptography course and would want to advertise it on her blog.
She currently targets audiences originating from various countries.
In the past, she ran ads to advertise a related course on the same blog and collected data in the process.
She would now like to employ your services as a Data Science Consultant to help her identify which individuals are most likely to click on her ads.


# 1. DEFINING THE QUESTION

## i) Specifying the Data Analytic Question

To be able to help a Kenyan entrepreneur identify which individuals are most likely to click on her ads.

## ii) Defining the Metric for Success

To perform univariate and bivariate data analysis and based on that, provide recommendations and insights on which individuals are most likely to click on her ads.

## iii) Understanding the Context

The entrepreneur wants to understand which factors determine whether an individual will click on her ads such as their age, gender, time spent on the site, daily internet usage, the city and the country they are from.

## iv) Recording the Experimental Design

1)  Define the question, the metric for success, the context, experimental design taken and the appropriateness of the available data to answer the given question
2)  Read the dataset into our environment (RStudio)
3)  Preview the dataset
4)  Find and deal with outliers, anomalies, and missing data within the dataset
5)  Perform univariate and bivariate analysis
6)  From your insights provide a conclusion and recommendation


# 2. IMPORTING THE RELEVANT LIBRARIES


```r
library(data.table)
```


# 3. LOADING THE DATASET

First we set the working directory by choosing the directory in which our dataset is in.
Afterwards we read the csv file and view it in our environment.
OR 
Alternatively read the URL and view the csv file in our environment.


```r
# Set working directory 
# setwd(choose.dir())

# Read the csv file from the URL
advertising = fread('http://bit.ly/IPAdvertisingData')

# View the dataset in our environment
View(advertising)
```


# 4. PREVIEWING THE DATASET

i)  The top 6 rows in our dataset


```r
head(advertising)
```

```
##    Daily Time Spent on Site Age Area Income Daily Internet Usage
## 1:                    68.95  35    61833.90               256.09
## 2:                    80.23  31    68441.85               193.77
## 3:                    69.47  26    59785.94               236.50
## 4:                    74.15  29    54806.18               245.89
## 5:                    68.37  35    73889.99               225.58
## 6:                    59.99  23    59761.56               226.74
##                            Ad Topic Line           City Male    Country
## 1:    Cloned 5thgeneration orchestration    Wrightburgh    0    Tunisia
## 2:    Monitored national standardization      West Jodi    1      Nauru
## 3:      Organic bottom-line service-desk       Davidton    0 San Marino
## 4: Triple-buffered reciprocal time-frame West Terrifurt    1      Italy
## 5:         Robust logistical utilization   South Manuel    0    Iceland
## 6:       Sharable client-driven software      Jamieberg    1     Norway
##              Timestamp Clicked on Ad
## 1: 2016-03-27 00:53:11             0
## 2: 2016-04-04 01:39:02             0
## 3: 2016-03-13 20:35:42             0
## 4: 2016-01-10 02:31:19             0
## 5: 2016-06-03 03:36:18             0
## 6: 2016-05-19 14:30:17             0
```

ii) The bottom 6 rows in our dataset


```r
tail(advertising)
```

```
##    Daily Time Spent on Site Age Area Income Daily Internet Usage
## 1:                    43.70  28    63126.96               173.01
## 2:                    72.97  30    71384.57               208.58
## 3:                    51.30  45    67782.17               134.42
## 4:                    51.63  51    42415.72               120.37
## 5:                    55.55  19    41920.79               187.95
## 6:                    45.01  26    29875.80               178.35
##                           Ad Topic Line          City Male
## 1:        Front-line bifurcated ability  Nicholasland    0
## 2:        Fundamental modular algorithm     Duffystad    1
## 3:      Grass-roots cohesive monitoring   New Darlene    1
## 4:         Expanded intangible solution South Jessica    1
## 5: Proactive bandwidth-monitored policy   West Steven    0
## 6:      Virtual 5thgeneration emulation   Ronniemouth    0
##                   Country           Timestamp Clicked on Ad
## 1:                Mayotte 2016-04-04 03:57:48             1
## 2:                Lebanon 2016-02-11 21:49:00             1
## 3: Bosnia and Herzegovina 2016-04-22 02:07:01             1
## 4:               Mongolia 2016-02-01 17:24:57             1
## 5:              Guatemala 2016-03-24 02:35:54             0
## 6:                 Brazil 2016-06-03 21:43:21             1
```

iii) The shape of the dataset


```r
# Dimensions of the dataset
dim(advertising)
```

```
## [1] 1000   10
```

The advertising dataset has 1000 rows and 10 columns.

iv) The datatypes of the columns in our dataset


```r
# Structure of the dataset
str(advertising)
```

```
## Classes 'data.table' and 'data.frame':	1000 obs. of  10 variables:
##  $ Daily Time Spent on Site: num  69 80.2 69.5 74.2 68.4 ...
##  $ Age                     : int  35 31 26 29 35 23 33 48 30 20 ...
##  $ Area Income             : num  61834 68442 59786 54806 73890 ...
##  $ Daily Internet Usage    : num  256 194 236 246 226 ...
##  $ Ad Topic Line           : chr  "Cloned 5thgeneration orchestration" "Monitored national standardization" "Organic bottom-line service-desk" "Triple-buffered reciprocal time-frame" ...
##  $ City                    : chr  "Wrightburgh" "West Jodi" "Davidton" "West Terrifurt" ...
##  $ Male                    : int  0 1 0 1 0 1 0 1 1 1 ...
##  $ Country                 : chr  "Tunisia" "Nauru" "San Marino" "Italy" ...
##  $ Timestamp               : POSIXct, format: "2016-03-27 00:53:11" "2016-04-04 01:39:02" ...
##  $ Clicked on Ad           : int  0 0 0 0 0 0 0 1 0 0 ...
##  - attr(*, ".internal.selfref")=<externalptr>
```

We can tell that 3 columns are of the type integer, 3 are of the type number and 4 columns are of the character type.


# 5. CLEANING THE DATASET

## Checking for null values in the dataset


```r
#is.na(advertising)

# Sum of null values in each column
colSums(is.na(advertising))
```

```
## Daily Time Spent on Site                      Age              Area Income 
##                        0                        0                        0 
##     Daily Internet Usage            Ad Topic Line                     City 
##                        0                        0                        0 
##                     Male                  Country                Timestamp 
##                        0                        0                        0 
##            Clicked on Ad 
##                        0
```

We can conclude that there are no missing values in our dataset.

## Checking for duplicate values in the dataset.


```r
# Checking the number of duplicated rows
duplicated_rows <- advertising[duplicated(advertising),]
duplicated_rows
```

```
## Empty data.table (0 rows and 10 cols): Daily Time Spent on Site,Age,Area Income,Daily Internet Usage,Ad Topic Line,City...
```

We can conclude that there are also no duplicate values in our dataset.

## Checking for outliers

We can check for outliers using the boxplots.

### i) Daily Time Spent on Site column.


```r
# Daily Time Spent on Site
boxplot(advertising$"Daily Time Spent on Site")
```

![](Week12-Independent-Project_files/figure-html/time-1.png)<!-- -->

From the boxplot, we can tell that there are no outliers in the 'Daily Time spent on site' column.

### ii) Age Column


```r
# Age
boxplot(advertising$Age)
```

![](Week12-Independent-Project_files/figure-html/unnamed-chunk-1-1.png)<!-- -->

We don't have outliers in the age column.

### iii) Area Income column


```r
# Area Income
boxplot(advertising$"Area Income")
```

![](Week12-Independent-Project_files/figure-html/income-1.png)<!-- -->

```r
# boxplot.stats(advertising$Area.Income)$out
```

There are outliers in the area income column.
We decided not to drop them because they are an actual representation of the income people in the area earn.

### iv) Daily Internet Usage


```r
# Daily Internet Usage
boxplot(advertising$"Daily Internet Usage")
```

![](Week12-Independent-Project_files/figure-html/units-1.png)<!-- -->

There are no outliers in the daily internet usage column.


# 6. EXPLORATORY DATA ANALYSIS

## A. UNIVARIATE DATA ANALYSIS

### Measures of Central Tendency

#### i) Mean


```r
mean(advertising$"Daily Time Spent on Site")
```

```
## [1] 65.0002
```

The average time spent on the site daily is 65 minutes.


```r
mean(advertising$Age)
```

```
## [1] 36.009
```

The average age of individuals in our dataset is 36 years old.


```r
mean(advertising$"Daily Internet Usage")
```

```
## [1] 180.0001
```

The average daily internet usage is 180 units.

#### ii) Median


```r
median(advertising$"Daily Time Spent on Site")
```

```
## [1] 68.215
```

The median time spent on site daily is 68.215 minutes.


```r
median(advertising$Age)
```

```
## [1] 35
```

The median age is 35 years old.


```r
median(advertising$"Daily Internet Usage")
```

```
## [1] 183.13
```

The median daily internet usage is 183.13 units.

#### iii) Mode


```r
# Define a function for getting the mode 
getmode <- function(v) {
  uniqv <- unique(v)
  uniqv[which.max(tabulate(match(v, uniqv)))]
}
getmode(advertising$"Daily Time Spent on Site")
```

```
## [1] 62.26
```

The mode for the daily time spent on the site is 62.26 minutes.


```r
getmode(advertising$Age)
```

```
## [1] 31
```

The mode for the age is 31 years old.


```r
getmode(advertising$"Daily Internet Usage")
```

```
## [1] 167.22
```

The mode for the daily internet usage is 167.22 units.


### Measures of Dispersion

#### i) Range


```r
range(advertising$"Daily Time Spent on Site")
```

```
## [1] 32.60 91.43
```

The range of daily time spent on the site is between 32.60 and 91.43 minutes


```r
range(advertising$Age)
```

```
## [1] 19 61
```

The age range is between 19 and 61 years old.


```r
range(advertising$"Daily Internet Usage")
```

```
## [1] 104.78 269.96
```

The range of daily internet usage is between 104.78 and 269.96 units.

#### ii) Quantiles


```r
quantile(advertising$"Daily Time Spent on Site")
```

```
##      0%     25%     50%     75%    100% 
## 32.6000 51.3600 68.2150 78.5475 91.4300
```

The first quantile in the daily time spent is 51.36 minutes.
The third quantile in the daily time spent on site is 78.55 minutes.


```r
quantile(advertising$Age)
```

```
##   0%  25%  50%  75% 100% 
##   19   29   35   42   61
```

The first quantile in age is 29 years old.
The third quantile in age is 42 years old.


```r
quantile(advertising$"Daily Internet Usage")
```

```
##       0%      25%      50%      75%     100% 
## 104.7800 138.8300 183.1300 218.7925 269.9600
```

The first quantile in the daily internet usage is 138.83 units.
The third quantile in the daily internet usage is 218.79 units.

#### iii) Variance


```r
var(advertising$"Daily Time Spent on Site")
```

```
## [1] 251.3371
```

The variance in the daily time spent on site is 251.33


```r
var(advertising$Age)
```

```
## [1] 77.18611
```

The variance in the age is 77.18


```r
var(advertising$"Daily Internet Usage")
```

```
## [1] 1927.415
```

The variance in the daily internet usage is 1927.415

#### iv) Standard Deviation


```r
sd(advertising$"Daily Time Spent on Site")
```

```
## [1] 15.85361
```

The standard deviation in the daily time spent on site is 15.85361


```r
sd(advertising$Age)
```

```
## [1] 8.785562
```

The standard deviation in the age is 8.785562


```r
sd(advertising$"Daily Internet Usage")
```

```
## [1] 43.90234
```

The standard deviation in the daily internet usage is 43.90234


### Frequency Tables


```r
# Gender Frequency Table
# 0 symbolizes female while 1 is male
gender <- table(advertising$Male)
gender
```

```
## 
##   0   1 
## 519 481
```

From the frequency table above, we can tell that there are 519 females and 481 males in our dataset.


```r
# Clicked on Ad Frequency Table
# 0 means the individual did not click on the ad, 1 means the individual clicked on an ad
clicked <- table(advertising$"Clicked on Ad")
clicked
```

```
## 
##   0   1 
## 500 500
```

From the frequency table above, we can tell that our dataset is balanced in the sense that 500 individuals clicked on the ad while 500 did not click on the ads.


```r
# Country Frequency Table
country <- (table(advertising$Country))

# Sort the table so as to find the country with the most individuals in our dataset
sorted_country <- sort(country, decreasing = TRUE)
head(sorted_country)
```

```
## 
## Czech Republic         France    Afghanistan      Australia         Cyprus 
##              9              9              8              8              8 
##         Greece 
##              8
```

From the frequency table above, we can tell that both Czech Republic and France had 9 individuals each while Afghanistan, Australia, Cyprus and Greece all had 8 individuals each.


```r
# City Frequency Table
city <- table(advertising$City)

# Sort the table so as to find the city with the most individuals in our dataset
sorted_city <- sort(city, decreasing = TRUE)
head(sorted_city)
```

```
## 
##       Lisamouth    Williamsport Benjaminchester       East John    East Timothy 
##               3               3               2               2               2 
##        Johnstad 
##               2
```

From the frequency table, we can tell that both Lisamouth and Williamsport both had 3 individuals each while Benjaminchester, East John, East Timothy and Johnstad all had 2 individuals each.


```r
# Age Frequency Table
age <- table(advertising$Age)

# Sort the table so as to find the age with the most individuals
sorted_age <- sort(age, decreasing = TRUE)
head(sorted_age)
```

```
## 
## 31 36 28 29 33 30 
## 60 50 48 48 43 39
```

From the frequency table, we can tell that individuals aged between 28 and 36 years old are the most in our dataset.


### Graphical Plots

#### i) Bar Charts

![](Week12-Independent-Project_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

From the bar chart above, we can tell that age 31 had the highest frequency in the dataset.

![](Week12-Independent-Project_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

From the bar chart, we can tell that 0(female) had more count that 1(male) in our dataset.


#### ii) Histograms

![](Week12-Independent-Project_files/figure-html/histogram-1.png)<!-- -->

From the histogram, we can also tell that age 25 - 35 had the highest frequency in the dataset.


## B. BIVARIATE DATA ANALYSIS

### Covariance


```r
# We can find the covariance between age and the daily time spent on the site
age <- advertising$Age
time <- advertising$"Daily Time Spent on Site"

cov(age, time)
```

```
## [1] -46.17415
```

There is a negative covariance between age and the daily time spent on the site which means that the older a person is, the less time they spend on the site daily.


```r
age <- advertising$Age
units <- advertising$"Daily Internet Usage"

cov(age, units)
```

```
## [1] -141.6348
```

There is a negative covariance between age and the daily internet usage which means that the older a person is, the less units they use on internet daily.


### Correlation


```r
# We will use the age and time variables that we created earlier for correlation
cor(age, time)
```

```
## [1] -0.3315133
```

There is a negative linear relationship between age and the daily time spent on the site.


```r
# We will use the age and units variables that we created earlier for correlation
cor(age, units)
```

```
## [1] -0.3672086
```

There is a negative linear relationship between age and the daily internet usage.


### Correlation Matrix

First we load the corrplot library which enables us to plot a correlation matrix.


```r
library(corrplot) # This library allows us to plot correlation.
```

```
## corrplot 0.90 loaded
```

We go ahead to create a variable that holds the numerical columns in our dataset.


```r
# Create a subset of the numerical columns in our dataset
numerical <- subset(advertising, select = c("Daily Time Spent on Site", "Age", "Daily Internet Usage", "Area Income"))
```

Plot a correlation matrix for the numerical variables in our dataset.


```r
cor(numerical)
```

```
##                          Daily Time Spent on Site        Age
## Daily Time Spent on Site                1.0000000 -0.3315133
## Age                                    -0.3315133  1.0000000
## Daily Internet Usage                    0.5186585 -0.3672086
## Area Income                             0.3109544 -0.1826050
##                          Daily Internet Usage Area Income
## Daily Time Spent on Site            0.5186585   0.3109544
## Age                                -0.3672086  -0.1826050
## Daily Internet Usage                1.0000000   0.3374955
## Area Income                         0.3374955   1.0000000
```


### Scatter Plots

![](Week12-Independent-Project_files/figure-html/scatter-1.png)<!-- -->

From the scatter plot, we can hardly tell the relationship between age and the daily time spent on the site as it is very scattered and thus is difficult to conclude anything based on the plot.

![](Week12-Independent-Project_files/figure-html/unnamed-chunk-7-1.png)<!-- -->

From this scatter plot, we can determine any relationship between the age and the daily internet usage.


# 7. CONCLUSIONS

From the univariate data analysis, we can conclude that: 

- There were more females than males in our dataset.
- The dataset was balanced in the sense that 500 individuals clicked on the ads while 500 individuals did not click on the ads.
- Individuals who are between 28 and 36 years old were the most in our dataset.
- Czech Republic and France both had the highest number of individuals (9) in the dataset.
- Lisamouth and Williamsport cities both had the highest number of individuals (3) in the dataset.

From the bivariate data analysis, we can conclude that: 

- There is a negative covariance and correlation between age and daily time spent on the site which means that the older an individual is, the less time they spend on the site.
- There is also a negative covariance and correlation between age and the daily internet usage which means that the younger an individual is, the higher the internet usage is as compared to an older individual.


# 8. RECOMMENDATIONS

-   We recommend that she creates an ad that targets individuals aged between 25 and 35 years old seeing as they are the most in our dataset.
-   We recommend that she focuses her attention more on the youth as they use the internet more and spend more time on the site as compared to the older individuals.
