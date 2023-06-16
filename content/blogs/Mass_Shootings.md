---
categories:  
- ""    #the front matter should be like the one found in, e.g., blog2.md. It cannot be like the normal Rmd we used
- ""
date: "2023-06-15"
description: Mass Shootings # the title that will show up once someone gets to this page
draft: false
image: MassShootings.jpg # save picture in \static\img\blogs. Acceptable formats= jpg, jpeg, or png . Your iPhone pics wont work

keywords: ""
slug: Mass_Shootings # slug is the shorthand URL address... no spaces plz
title: Mass Shootings
---








# Mass Shootings
We are going to first analyse the data to understand what type of information we can get from the file.


```
## Rows: 125
## Columns: 14
## $ case                 <chr> "Oxford High School shooting", "San Jose VTA shoo…
## $ year                 <dbl> 2021, 2021, 2021, 2021, 2021, 2021, 2020, 2020, 2…
## $ month                <chr> "Nov", "May", "Apr", "Mar", "Mar", "Mar", "Mar", …
## $ day                  <dbl> 30, 26, 15, 31, 22, 16, 16, 26, 10, 6, 31, 4, 3, …
## $ location             <chr> "Oxford, Michigan", "San Jose, California", "Indi…
## $ summary              <chr> "Ethan Crumbley, a 15-year-old student at Oxford …
## $ fatalities           <dbl> 4, 9, 8, 4, 10, 8, 4, 5, 4, 3, 7, 9, 22, 3, 12, 5…
## $ injured              <dbl> 7, 0, 7, 1, 0, 1, 0, 0, 3, 8, 25, 27, 26, 12, 4, …
## $ total_victims        <dbl> 11, 9, 15, 5, 10, 9, 4, 5, 7, 11, 32, 36, 48, 15,…
## $ location_type        <chr> "School", "Workplace", "Workplace", "Workplace", …
## $ male                 <lgl> TRUE, TRUE, TRUE, TRUE, TRUE, TRUE, TRUE, TRUE, T…
## $ age_of_shooter       <dbl> 15, 57, 19, NA, 21, 21, 31, 51, NA, NA, 36, 24, 2…
## $ race                 <chr> NA, NA, "White", NA, NA, "White", NA, "Black", "B…
## $ prior_mental_illness <chr> NA, "Yes", "Yes", NA, "Yes", NA, NA, NA, NA, NA, …
```

Then we are going to generate our first bar chart that identifies the number of mass shooters associated with each race category. The bars are sorted from highest to lowest and each bar show its number.


```r
#Create the bar chat
bar_chart <- mass_shootings %>% 
  group_by(race) %>% 
  summarise(Count = n()) %>% 
  arrange(desc(Count)) %>% 
  ggplot(aes(x = race, y = Count, fill = race )) +
  geom_bar(stat = "identity")
  labs(x = "Race", y = "Number of mass shooters", title = "Number of mass shooters by Race") +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 90, vjust = 0.5, hjust = 1))
```

```
## NULL
```

```r
#Show the result
bar_chart
```

<img src="/blogs/Mass_Shootings_files/figure-html/unnamed-chunk-2-1.png" width="648" style="display: block; margin: auto;" />

