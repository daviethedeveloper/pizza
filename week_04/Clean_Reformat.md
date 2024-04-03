---
title: "Clean and Reformat"
author: "David Pineda"
format:
  html:
    keep-md: true
editor: visual
---

# Import Packages

::: cell
``` {.r .cell-code}
library(dplyr)
```

::: {.cell-output .cell-output-stderr}
```         

Attaching package: 'dplyr'
```
:::

::: {.cell-output .cell-output-stderr}
```         
The following objects are masked from 'package:stats':

    filter, lag
```
:::

::: {.cell-output .cell-output-stderr}
```         
The following objects are masked from 'package:base':

    intersect, setdiff, setequal, union
```
:::

``` {.r .cell-code}
library(tidyr)
library(ggplot2)
library(readr)
library(stringr)
library(forcats)
```
:::

::: cell
``` {.r .cell-code}
url <- "https://raw.githubusercontent.com/byuistats/data/master/Dart_Expert_Dow_6month_anova/Dart_Expert_Dow_6month_anova.RDS"


data <- read_rds(url)
```
:::

::: cell
``` {.r .cell-code}
data
```

::: {.cell-output .cell-output-stdout}
```         
# A tibble: 300 × 3
   contest_period             variable value
   <chr>                      <chr>    <dbl>
 1 January-June1990           PROS      12.7
 2 February-July1990          PROS      26.4
 3 March-August1990           PROS       2.5
 4 April-September1990        PROS     -20  
 5 May-October1990            PROS     -37.8
 6 June-November1990          PROS     -33.3
 7 July-December1990          PROS     -10.2
 8 August1990-January1991     PROS     -20.3
 9 September1990-February1991 PROS      38.9
10 October1990-March1991      PROS      20.2
# ℹ 290 more rows
```
:::
:::

Clean february

::: cell
``` {.r .cell-code}
data <- data %>% 
  mutate(contest_period = str_replace(contest_period, "July1993-Dec.1993", "July1993-December1993")) %>%
  mutate(contest_period = str_replace(contest_period, "Febuary-July1995", "February-July1995")) %>%
  mutate(contest_period = str_replace(contest_period, "September1994-Febuary1995", "September1994-February1995"))
```
:::

::: cell
``` {.r .cell-code}
data <- data %>%
  mutate(
    month_end = gsub(".*-(.*?)\\d+", "\\1", contest_period),
    year_end = as.integer(gsub(".*([0-9]{4})", "\\1", contest_period))
  )
```
:::

::: cell
``` {.r .cell-code}
data
```

::: {.cell-output .cell-output-stdout}
```         
# A tibble: 300 × 5
   contest_period             variable value month_end year_end
   <chr>                      <chr>    <dbl> <chr>        <int>
 1 January-June1990           PROS      12.7 June          1990
 2 February-July1990          PROS      26.4 July          1990
 3 March-August1990           PROS       2.5 August        1990
 4 April-September1990        PROS     -20   September     1990
 5 May-October1990            PROS     -37.8 October       1990
 6 June-November1990          PROS     -33.3 November      1990
 7 July-December1990          PROS     -10.2 December      1990
 8 August1990-January1991     PROS     -20.3 January       1991
 9 September1990-February1991 PROS      38.9 February      1991
10 October1990-March1991      PROS      20.2 March         1991
# ℹ 290 more rows
```
:::
:::

::: cell
``` {.r .cell-code}
df <- data %>%
  filter(variable == "DJIA")


df
```

::: {.cell-output .cell-output-stdout}
```         
# A tibble: 100 × 5
   contest_period             variable value month_end year_end
   <chr>                      <chr>    <dbl> <chr>        <int>
 1 January-June1990           DJIA       2.5 June          1990
 2 February-July1990          DJIA      11.5 July          1990
 3 March-August1990           DJIA      -2.3 August        1990
 4 April-September1990        DJIA      -9.2 September     1990
 5 May-October1990            DJIA      -8.5 October       1990
 6 June-November1990          DJIA     -12.8 November      1990
 7 July-December1990          DJIA      -9.3 December      1990
 8 August1990-January1991     DJIA      -0.8 January       1991
 9 September1990-February1991 DJIA      11   February      1991
10 October1990-March1991      DJIA      15.8 March         1991
# ℹ 90 more rows
```
:::
:::

::: cell
``` {.r .cell-code}
tidy_data_selected <- df %>%
  select(value, month_end, year_end)
```
:::

::: cell
``` {.r .cell-code}
saveRDS(tidy_data_selected, "clean_data.rds")
```
:::

::: cell
``` {.r .cell-code}
df <- readRDS("clean_data.rds")
```
:::

::: cell
``` {.r .cell-code}
df
```

::: {.cell-output .cell-output-stdout}
```         
# A tibble: 100 × 3
   value month_end year_end
   <dbl> <chr>        <int>
 1   2.5 June          1990
 2  11.5 July          1990
 3  -2.3 August        1990
 4  -9.2 September     1990
 5  -8.5 October       1990
 6 -12.8 November      1990
 7  -9.3 December      1990
 8  -0.8 January       1991
 9  11   February      1991
10  15.8 March         1991
# ℹ 90 more rows
```
:::
:::

::: cell
``` {.r .cell-code}
summary(df)
```

::: {.cell-output .cell-output-stdout}
```         
     value          month_end            year_end   
 Min.   :-13.100   Length:100         Min.   :1990  
 1st Qu.:  1.575   Class :character   1st Qu.:1992  
 Median :  7.000   Mode  :character   Median :1994  
 Mean   :  6.793                      Mean   :1994  
 3rd Qu.: 13.150                      3rd Qu.:1996  
 Max.   : 22.500                      Max.   :1998  
```
:::
:::

::: cell
``` {.r .cell-code}
df <- df %>%
  mutate(month = factor(month_end, levels = month.name)) %>%
  arrange(month) %>%
  arrange(year_end)
df
```

::: {.cell-output .cell-output-stdout}
```         
# A tibble: 100 × 4
   value month_end year_end month    
   <dbl> <chr>        <int> <fct>    
 1   2.5 June          1990 June     
 2  11.5 July          1990 July     
 3  -2.3 August        1990 August   
 4  -9.2 September     1990 September
 5  -8.5 October       1990 October  
 6 -12.8 November      1990 November 
 7  -9.3 December      1990 December 
 8  -0.8 January       1991 January  
 9  11   February      1991 February 
10  15.8 March         1991 March    
# ℹ 90 more rows
```
:::
:::

::: cell
``` {.r .cell-code}
# Check unique values in the "month_end" column
unique(df$month_end)
```

::: {.cell-output .cell-output-stdout}
```         
 [1] "June"      "July"      "August"    "September" "October"   "November" 
 [7] "December"  "January"   "February"  "March"     "April"     "May"      
```
:::
:::

::: cell
``` {.r .cell-code}
result <- df %>%
  pivot_wider(names_from = year_end, values_from = value) %>%
  mutate_all(~ replace(., is.na(.), "-")) %>%
  arrange(month) %>%
  select(-month_end) %>%
  rename(Month = month)
```

::: {.cell-output .cell-output-stderr}
```         
Warning: There was 1 warning in `mutate()`.
ℹ In argument: `month = (structure(function (..., .x = ..1, .y = ..2, . = ..1)
  ...`.
Caused by warning in `[<-.factor`:
! invalid factor level, NA generated
```
:::

``` {.r .cell-code}
result
```

::: {.cell-output .cell-output-stdout}
```         
# A tibble: 12 × 10
   Month     `1990` `1991` `1992` `1993` `1994` `1995` `1996` `1997` `1998`
   <fct>     <chr>  <chr>  <chr>  <chr>  <chr>  <chr>  <chr>  <chr>  <chr> 
 1 January   -      -0.8   6.5    -0.8   11.2   1.8    15     19.6   -0.3  
 2 February  -      11     8.6    2.5    5.5    3.2    15.6   20.1   10.7  
 3 March     -      15.8   7.2    9      1.6    7.3    18.4   9.6    7.6   
 4 April     -      16.2   10.6   5.8    0.5    12.8   14.8   15.3   22.5  
 5 May       -      17.3   17.6   6.7    1.3    19.5   9      13.3   10.6  
 6 June      2.5    17.7   3.6    7.7    -6.2   16     10.2   16.2   15    
 7 July      11.5   7.6    4.2    3.7    -5.3   19.6   1.3    20.8   7.1   
 8 August    -2.3   4.4    -0.3   7.3    1.5    15.3   0.6    8.3    -13.1 
 9 September -9.2   3.4    -0.1   5.2    4.4    14     5.8    20.2   -11.8 
10 October   -8.5   4.4    -5     5.7    6.9    8.2    7.2    3      -     
11 November  -12.8  -3.3   -2.8   4.9    -0.3   13.1   15.1   3.8    -     
12 December  -9.3   6.6    0.2    8      3.6    9.3    15.5   -0.7   -     
```
:::
:::
