---
title: "Climdex.pcic Package"
author: "Mattia Fabris"
date: "2025-05-21"
output: html_document
---

## The Package

The climdex.pcic package provides an implementation of the 27 core climate change indices of the ETCCDI.

Climdex.pcic is not available from CRAN at this time.
To install the package I used the following commands:



```{r, eval=F}
install.packages('PCICt')
install.packages('https://pacificclimate.org/R/climdex.pcic_1.1-11.tar.gz')
library(climdex.pcic)
``` 


## Parse the dates into PCICt

Create a climdexInput object from some data already loaded in and ready to go.


```{r, eval=F}
tmax.dates <- as.PCICt(do.call(paste, ec.1018935.tmax[,c("year",
                                                         "jday")]), format="%Y %j", cal="gregorian")
tmin.dates <- as.PCICt(do.call(paste, ec.1018935.tmin[,c("year",
                                                         "jday")]), format="%Y %j", cal="gregorian")
prec.dates <- as.PCICt(do.call(paste, ec.1018935.prec[,c("year",
                                                         "jday")]), format="%Y %j", cal="gregorian")
``` 

Load the data in


```{r, eval=F} 
ci <- climdexInput.raw(ec.1018935.tmax$MAX_TEMP,
                       ec.1018935.tmin$MIN_TEMP, ec.1018935.prec$ONE_DAY_PRECIPITATION,
                       tmax.dates, tmin.dates, prec.dates, base.range=c(1971, 2000))
``` 


## Compute the indeces
### 1. Frost days, FD index

Annual count of days where daily minimum temperature drops below 0 degrees Celsius

```{r, eval=F} 
fd <- climdex.fd(ci)
df_fd <- data.frame(
  year = as.numeric(names(fd)),
  value = as.numeric(fd)
)
```
```{r, eval=T} 
library(ggplot2)
ggplot(df_fd, aes(x = factor(year), y = value)) +
  geom_bar(stat = "identity", fill = "green") +
  labs(
    title = "FD: frost days, days where min T < 0",
    x = "Anno",
    y = "Days"
  )
```
<img src="Rplot.png" width="100%" />

### 2. Summer days, SU index

Annual count of days where daily maximum temperature exceeds 25 degrees Celsius
```
su <- climdex.su(ci)
```
<img src="Rplot01.png" width="100%" />

### 3. Icing days, ID index

Annual count of days where daily maximum temperature is below 0 degrees Celsius
```
id <- climdex.id(ci)
```
<img src="Rplot02.png" width="100%" />

### 4. Tropical nights, TR index

Annual count of days where daily minimum temperature stays above 20 degrees Celsius
```
tr <- climdex.tr(ci)
```

## 5. Growing season length, GSL index

The number of days between the start of the first spell of warm days in the first half of the year, 
and the start of the first spell of cold days in the second half of the year.
Spells of warm days: six or more days with mean temperature above 5 degrees Celsius.
Spells of cold days: six or more days with a mean temperature below 5 degrees Celsius.
There are 3 modes for test purpose only:
- ’GSL_first’ returns the first growing season found; 
- ’GSL_max’ returns the longest growing season found;  
- ’GSL_sum’ returns the total length of all growing seasons found.
```
gsl <- climdex.gsl(ci)
a <- climdex.gsl(ci, gsl.mode = c("GSL", "GSL_first", "GSL_max", "GSL_sum"))

b <- growing.season.length(ci@data$tavg, ci@date.factors$annual, ci@dates,
                           ci@northern.hemisphere, gsl.mode="GSL") *
  ci@namasks$annual$tavg
```
<img src="Rplot3.png" width="100%" />

### 6. Monthly maximum of daily maximum temperature, TXx index 

Computes the monthly or annual maximum of daily maximum temperature.

In this case, I decided to compute only annual data for simplicity. To enter monthly data, simply indicate ‘monthly’ in brackets in the frequency field.
```
txx <- climdex.txx(ci, freq = c("annual"))
```
<img src="Rplot04.png" width="100%" />

### 7. 
