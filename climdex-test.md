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
### 1.Frost days, FD index

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



