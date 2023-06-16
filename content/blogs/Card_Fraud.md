---
categories:  
- ""    #the front matter should be like the one found in, e.g., blog2.md. It cannot be like the normal Rmd we used
- ""
date: "2023-06-15"
description: Card Fraud # the title that will show up once someone gets to this page
draft: false
image: CardFraud.jpg # save picture in \static\img\blogs. Acceptable formats= jpg, jpeg, or png . Your iPhone pics wont work

keywords: ""
slug: Card_Fraud # slug is the shorthand URL address... no spaces plz
title: Card Fraud
---





```r
library(tidyverse)
library(wbstats)
library(skimr)
library(countrycode)
library(here)
```


We will be using a dataset with credit card transactions containing legitimate and fraud transactions. Fraud is typically well below 1% of all transactions, so a naive model that predicts that all transactions are legitimate and not fraudulent would have an accuracy of well over 99%– pretty good, no? (well, not quite as we will see later in the course)

You can read more on credit card fraud on Credit Card Fraud Detection Using Weighted Support Vector Machine

The dataset we will use consists of credit card transactions and it includes information about each transaction including customer details, the merchant and category of purchase, and whether or not the transaction was a fraud.

Obtain the data

The dataset is too large to be hosted on Canvas or Github, so please download it from dropbox https://www.dropbox.com/sh/q1yk8mmnbbrzavl/AAAxzRtIhag9Nc_hODafGV2ka?dl=0 and save it in your dsb repo, under the data folder


```
## Rows: 671,028
## Columns: 14
## $ trans_date_trans_time <dttm> 2019-02-22 07:32:58, 2019-02-16 15:07:20, 2019-…
## $ trans_year            <dbl> 2019, 2019, 2019, 2019, 2019, 2019, 2019, 2020, …
## $ category              <chr> "entertainment", "kids_pets", "personal_care", "…
## $ amt                   <dbl> 7.79, 3.89, 8.43, 40.00, 54.04, 95.61, 64.95, 3.…
## $ city                  <chr> "Veedersburg", "Holloway", "Arnold", "Apison", "…
## $ state                 <chr> "IN", "OH", "MO", "TN", "CO", "GA", "MN", "AL", …
## $ lat                   <dbl> 40.1, 40.0, 38.4, 35.0, 39.5, 32.8, 48.6, 34.6, …
## $ long                  <dbl> -87.3, -81.0, -90.4, -85.0, -106.4, -83.2, -93.3…
## $ city_pop              <dbl> 4049, 128, 35439, 3730, 277, 1841, 136, 190178, …
## $ job                   <chr> "Development worker, community", "Child psychoth…
## $ dob                   <chr> "10/19/1959", "4/3/1946", "3/31/1985", "1/28/199…
## $ merch_lat             <dbl> 39.4, 39.7, 37.7, 34.5, 40.0, 32.9, 48.6, 35.1, …
## $ merch_long            <dbl> -87.5, -81.5, -91.4, -84.1, -106.9, -82.2, -94.1…
## $ is_fraud              <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, …
```

In this dataset, how likely are fraudulent transactions? Generate a table that summarizes the number and frequency of fraudulent transactions per year.


```r
#First we are going to create the table grouped by transaction year that calculates de number of frauds they were and how frequent they occured. As sated by the text, the number should be very low
Fraudulent_Transactions <- card_fraud %>% 
  group_by(trans_year) %>% 
  summarise(Number_Fraud = sum(is_fraud),Frequency = Number_Fraud/n())

#Show result
Fraudulent_Transactions
```

```
## # A tibble: 2 × 3
##   trans_year Number_Fraud Frequency
##        <dbl>        <dbl>     <dbl>
## 1       2019         2721   0.00568
## 2       2020         1215   0.00632
```

#How much money (in US$ terms) are fraudulent transactions costing the company? Generate a table that summarizes the total amount of legitimate and fraudulent transactions per year and calculate the % of fraudulent transactions, in US$ terms.


```r
#We are going to create a table that summarises the amount of money that was fraudulent or legitimate and calculate its share 

F_L_Transaction <- card_fraud %>% 
  group_by(trans_year) %>% 
  summarise(Leigitimate_Money = sum(amt*(1-is_fraud)),
            Fraudulent_Money = sum(amt*is_fraud),
            Share = (Fraudulent_Money/(Leigitimate_Money + Fraudulent_Money) * 100))
#Show result
F_L_Transaction
```

```
## # A tibble: 2 × 4
##   trans_year Leigitimate_Money Fraudulent_Money Share
##        <dbl>             <dbl>            <dbl> <dbl>
## 1       2019         32182901.         1423140.  4.23
## 2       2020         12925914.          651949.  4.80
```

#Generate a histogram that shows the distribution of amounts charged to credit card, both for legitimate and fraudulent accounts. Also, for both types of transactions, calculate some quick summary statistics.


```r
# First we are going to clasify the dataset for legitimate and fraudulent transactions
Legitimate <- card_fraud %>% 
  filter(is_fraud == 0)
Fraudulent <- card_fraud %>% 
  filter(is_fraud == 1)

# Then we are going to create the histograms
ggplot() +
  geom_histogram(data = Legitimate, aes(x = amt), fill = "blue", alpha = 0.7, binwidth = 30) +
  geom_histogram(data = Fraudulent, aes(x = amt), fill = "red", alpha = 0.7, binwidth = 30) +
  labs(x = "Amount (USD)", y = "Frequency", title = "Share of Amounts Charged to Credit Card") +
  scale_x_continuous(labels = scales::dollar_format(prefix = "$"), breaks = seq(0, 500, by = 50)) +
  scale_y_continuous(labels = scales::comma_format()) +
  theme_minimal()
```

<img src="/blogs/Card_Fraud_files/figure-html/unnamed-chunk-5-1.png" width="648" style="display: block; margin: auto;" />

```r
# Finally, we are going to calculate summary statistics for each
Legitimate_summary <- summary(Legitimate$amt)
Fraudulent_summary <- summary(Fraudulent$amt)

#Show results
Legitimate_summary
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##       1      10      47      68      82   27120
```

```r
Fraudulent_summary
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##       1     240     369     527     901    1334
```

