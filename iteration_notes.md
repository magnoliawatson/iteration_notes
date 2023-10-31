iteration_notes
================
2023-10-31

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.3     ✔ readr     2.1.4
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.0
    ## ✔ ggplot2   3.4.3     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.2     ✔ tidyr     1.3.0
    ## ✔ purrr     1.0.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(rvest)
```

    ## 
    ## Attaching package: 'rvest'
    ## 
    ## The following object is masked from 'package:readr':
    ## 
    ##     guess_encoding

``` r
set.seed(12345)
```

## lists

``` r
vec_numeric = 1:4
vec_char = c("My", "name", "is", "Jeff")

tibble(
  num = vec_numeric,
  char = vec_char)
```

    ## # A tibble: 4 × 2
    ##     num char 
    ##   <int> <chr>
    ## 1     1 My   
    ## 2     2 name 
    ## 3     3 is   
    ## 4     4 Jeff

different stuff with different lengths

``` r
l = 
  list(
vec_numeric = 1:5,
vec_char = LETTERS,
matrix = matrix(1:10, nrow = 5, ncol = 2),
summary = summary(rnorm(100))
)
```

accessing lists

``` r
l$vec_char
```

    ##  [1] "A" "B" "C" "D" "E" "F" "G" "H" "I" "J" "K" "L" "M" "N" "O" "P" "Q" "R" "S"
    ## [20] "T" "U" "V" "W" "X" "Y" "Z"

``` r
l[[2]]
```

    ##  [1] "A" "B" "C" "D" "E" "F" "G" "H" "I" "J" "K" "L" "M" "N" "O" "P" "Q" "R" "S"
    ## [20] "T" "U" "V" "W" "X" "Y" "Z"

``` r
l[["summary"]]
```

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ## -2.3804 -0.5901  0.4837  0.2452  0.9004  2.4771

## loops

``` r
list_norm_Samples = 
  list(
    a = rnorm(20, 1, 5),
    b = rnorm(20, 0 ,7),
    c = rnorm(20, 20, 1),
    d = rnorm(20, -45, 13)
  )

mean_and_sd = function(x) {
  
  if (!is.numeric(x)) {
    stop("Argument x should be numeric")
  } else if (length(x) == 1) {
    stop("Cannot be computed for length 1 vectors")
  }
  
  mean_x = mean(x)
  sd_x = sd(x)

  list(mean = mean_x, 
       sd = sd_x)
}
```

``` r
mean_and_sd(list_norm_Samples$a)
```

    ## $mean
    ## [1] 1.254022
    ## 
    ## $sd
    ## [1] 4.922161

``` r
mean_and_sd(list_norm_Samples$b)
```

    ## $mean
    ## [1] 0.6895262
    ## 
    ## $sd
    ## [1] 9.303807

``` r
mean_and_sd(list_norm_Samples$c)
```

    ## $mean
    ## [1] 19.75471
    ## 
    ## $sd
    ## [1] 0.9098072

``` r
mean_and_sd(list_norm_Samples$d)
```

    ## $mean
    ## [1] -44.07465
    ## 
    ## $sd
    ## [1] 13.97962

``` r
output = vector("list", length = 4)

for (i in 1:4) {
  output[[i]] = mean_and_sd(list_norm_Samples[[i]])
}
```

## use `map`

``` r
output = map(list_norm_Samples, mean_and_sd)

output_med = map(list_norm_Samples, median)

output_sum = map(list_norm_Samples, summary)
```

## create df

``` r
listcol_df = 
  tibble(
    name = c("a", "b", "c", "d"),
    samp = list_norm_Samples
  )
```

``` r
listcol_df %>% 
  pull(samp)
```

    ## $a
    ##  [1]  2.1196270 -4.7811167  3.1120926 -5.6237763  1.7054216 -1.6802400
    ##  [7] -0.5580304  8.7805482 -1.2401665  2.6056177 -5.1508612 -5.6202935
    ## [13]  7.3062114  7.5961586  0.5962312 -1.5254490  0.7392320  4.1443031
    ## [19] 11.9000120  0.6549135
    ## 
    ## $b
    ##  [1]  10.8140452   9.2501641   2.2550610  10.7166858  -2.9486779  -8.1117472
    ##  [7] -12.9175780   8.1012770 -14.8648492  -8.3722206  11.4953439   6.1855838
    ## [13]   3.6741312  -8.2926135  18.5905179  -7.3353960  -7.0778577   4.6824516
    ## [19]   0.9042411  -2.9580381
    ## 
    ## $c
    ##  [1] 18.85974 18.70628 19.40530 18.49919 20.01586 20.54017 18.45271 20.84965
    ##  [9] 20.89601 20.13869 18.38067 20.54840 20.19528 19.19350 19.89138 19.74905
    ## [17] 21.69935 19.65570 20.06777 19.34943
    ## 
    ## $d
    ##  [1] -51.33930 -41.05903 -48.14566 -51.26254 -57.89344 -48.64844 -36.77077
    ##  [8] -61.11764 -22.06392 -45.30784 -42.40103 -27.48649 -44.53104 -34.28045
    ## [15] -67.13473 -38.74765 -12.71385 -39.78226 -42.20270 -68.60426

``` r
mean_and_sd(listcol_df$samp[[1]])
```

    ## $mean
    ## [1] 1.254022
    ## 
    ## $sd
    ## [1] 4.922161

``` r
mean_and_sd(listcol_df$samp[[2]])
```

    ## $mean
    ## [1] 0.6895262
    ## 
    ## $sd
    ## [1] 9.303807

``` r
mean_and_sd(listcol_df$samp[[3]])
```

    ## $mean
    ## [1] 19.75471
    ## 
    ## $sd
    ## [1] 0.9098072

``` r
map(listcol_df$samp, mean_and_sd)
```

    ## $a
    ## $a$mean
    ## [1] 1.254022
    ## 
    ## $a$sd
    ## [1] 4.922161
    ## 
    ## 
    ## $b
    ## $b$mean
    ## [1] 0.6895262
    ## 
    ## $b$sd
    ## [1] 9.303807
    ## 
    ## 
    ## $c
    ## $c$mean
    ## [1] 19.75471
    ## 
    ## $c$sd
    ## [1] 0.9098072
    ## 
    ## 
    ## $d
    ## $d$mean
    ## [1] -44.07465
    ## 
    ## $d$sd
    ## [1] 13.97962

``` r
listcol_df %>% 
  mutate(
    mean_sd = map(samp, mean_and_sd),
    median = map(samp, median)) %>% 
  pull(mean_sd)
```

    ## $a
    ## $a$mean
    ## [1] 1.254022
    ## 
    ## $a$sd
    ## [1] 4.922161
    ## 
    ## 
    ## $b
    ## $b$mean
    ## [1] 0.6895262
    ## 
    ## $b$sd
    ## [1] 9.303807
    ## 
    ## 
    ## $c
    ## $c$mean
    ## [1] 19.75471
    ## 
    ## $c$sd
    ## [1] 0.9098072
    ## 
    ## 
    ## $d
    ## $d$mean
    ## [1] -44.07465
    ## 
    ## $d$sd
    ## [1] 13.97962

``` r
listcol_df %>% 
  mutate(
    mean_sd = map(samp, mean_and_sd),
    median = map(samp, median)) %>% 
  select(name, mean_sd) %>% 
  unnest(mean_sd)
```

    ## # A tibble: 8 × 2
    ##   name  mean_sd     
    ##   <chr> <named list>
    ## 1 a     <dbl [1]>   
    ## 2 a     <dbl [1]>   
    ## 3 b     <dbl [1]>   
    ## 4 b     <dbl [1]>   
    ## 5 c     <dbl [1]>   
    ## 6 c     <dbl [1]>   
    ## 7 d     <dbl [1]>   
    ## 8 d     <dbl [1]>

# issue here, should have separated mean and sd

### NSDUH

``` r
nsduh_url = "http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm"

nsduh_html = read_html(nsduh_url)

nsduh_import <- function(html, table_num, outcome_name) {
  
  table = 
    html |> 
    html_table() |> 
    nth(table_num) |>
    slice(-1) |> 
    select(-contains("P Value")) |>
    pivot_longer(
      -State,
      names_to = "age_year", 
      values_to = "percent") |>
    separate(age_year, into = c("age", "year"), sep = "\\(") |>
    mutate(
      year = str_replace(year, "\\)", ""),
      percent = str_replace(percent, "[a-c]$", ""),
      percent = as.numeric(percent),
      name = outcome_name) |>
    filter(!(State %in% c("Total U.S.", "Northeast", "Midwest", "South", "West")))
}

nsduh_import(nsduh_html, 1, "marj")
nsduh_import(nsduh_html, 4, "cocaine")
nsduh_import(nsduh_html, 5, "heroin")
```

import data using for a loop

``` r
table_input = list(1, 4, 5)
name_input = list("marj", "cocaine", "heroin")

output = vector("list", length = 3)

for(i in c(1:3)) {
  output[[i]] = nsduh_import(nsduh_html, table_input[[i]], name_input[[i]])
}

nsduh_df = bind_rows(output)
```

try again using maps

``` r
nsduh_import <- function(html, table_num) {
  
  table = 
    html |> 
    html_table() |> 
    nth(table_num) |>
    slice(-1) |> 
    select(-contains("P Value")) |>
    pivot_longer(
      -State,
      names_to = "age_year", 
      values_to = "percent") |>
    separate(age_year, into = c("age", "year"), sep = "\\(") |>
    mutate(
      year = str_replace(year, "\\)", ""),
      percent = str_replace(percent, "[a-c]$", ""),
      percent = as.numeric(percent)) |>
    filter(!(State %in% c("Total U.S.", "Northeast", "Midwest", "South", "West")))
}


nsduh_df = 
  tibble(
    name = c("marj", "cocaine", "heroin"), 
    number = c(1, 4, 5)
  ) %>% 
  mutate(table = map(number, nsduh_import, html = nsduh_html))

map(nsduh_df$number, nsduh_import, html = nsduh_html)
```

    ## [[1]]
    ## # A tibble: 510 × 4
    ##    State   age   year      percent
    ##    <chr>   <chr> <chr>       <dbl>
    ##  1 Alabama 12+   2013-2014    9.98
    ##  2 Alabama 12+   2014-2015    9.6 
    ##  3 Alabama 12-17 2013-2014    9.9 
    ##  4 Alabama 12-17 2014-2015    9.71
    ##  5 Alabama 18-25 2013-2014   27.0 
    ##  6 Alabama 18-25 2014-2015   26.1 
    ##  7 Alabama 26+   2013-2014    7.1 
    ##  8 Alabama 26+   2014-2015    6.81
    ##  9 Alabama 18+   2013-2014    9.99
    ## 10 Alabama 18+   2014-2015    9.59
    ## # ℹ 500 more rows
    ## 
    ## [[2]]
    ## # A tibble: 510 × 4
    ##    State   age   year      percent
    ##    <chr>   <chr> <chr>       <dbl>
    ##  1 Alabama 12+   2013-2014    1.23
    ##  2 Alabama 12+   2014-2015    1.22
    ##  3 Alabama 12-17 2013-2014    0.42
    ##  4 Alabama 12-17 2014-2015    0.41
    ##  5 Alabama 18-25 2013-2014    3.09
    ##  6 Alabama 18-25 2014-2015    3.2 
    ##  7 Alabama 26+   2013-2014    1.01
    ##  8 Alabama 26+   2014-2015    0.99
    ##  9 Alabama 18+   2013-2014    1.31
    ## 10 Alabama 18+   2014-2015    1.31
    ## # ℹ 500 more rows
    ## 
    ## [[3]]
    ## # A tibble: 510 × 4
    ##    State   age   year      percent
    ##    <chr>   <chr> <chr>       <dbl>
    ##  1 Alabama 12+   2013-2014    0.22
    ##  2 Alabama 12+   2014-2015    0.27
    ##  3 Alabama 12-17 2013-2014    0.1 
    ##  4 Alabama 12-17 2014-2015    0.08
    ##  5 Alabama 18-25 2013-2014    0.45
    ##  6 Alabama 18-25 2014-2015    0.64
    ##  7 Alabama 26+   2013-2014    0.19
    ##  8 Alabama 26+   2014-2015    0.23
    ##  9 Alabama 18+   2013-2014    0.23
    ## 10 Alabama 18+   2014-2015    0.29
    ## # ℹ 500 more rows

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(
    c("USW00094728", "USW00022534", "USS0023B17S"),
    var = c("PRCP", "TMIN", "TMAX"), 
    date_min = "2021-01-01",
    date_max = "2022-12-31") |>
  mutate(
    name = recode(
      id, 
      USW00094728 = "CentralPark_NY", 
      USW00022534 = "Molokai_HI",
      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) |>
  select(name, id, everything())
```

    ## Registered S3 method overwritten by 'hoardr':
    ##   method           from
    ##   print.cache_info httr

    ## using cached file: C:\Users\magno\AppData\Local/R/cache/R/rnoaa/noaa_ghcnd/USW00094728.dly

    ## date created (size, mb): 2023-10-31 11:07:06.897491 (8.556)

    ## file min/max dates: 1869-01-01 / 2023-10-31

    ## using cached file: C:\Users\magno\AppData\Local/R/cache/R/rnoaa/noaa_ghcnd/USW00022534.dly

    ## date created (size, mb): 2023-10-31 11:07:41.144466 (3.85)

    ## file min/max dates: 1949-10-01 / 2023-10-31

    ## using cached file: C:\Users\magno\AppData\Local/R/cache/R/rnoaa/noaa_ghcnd/USS0023B17S.dly

    ## date created (size, mb): 2023-10-31 11:07:52.102886 (0.999)

    ## file min/max dates: 1999-09-01 / 2023-10-31

``` r
weather_nest_df =
  weather_df%>% 
  nest(df = date:tmin)
```

can i regress `tmax` on `tmin` for each of these?

``` r
central_park_df = 
  weather_nest_df %>% 
  pull(df) %>% 
  nth(1)
```

``` r
lm(tmax ~ tmin, data = central_park_df)
```

    ## 
    ## Call:
    ## lm(formula = tmax ~ tmin, data = central_park_df)
    ## 
    ## Coefficients:
    ## (Intercept)         tmin  
    ##       7.514        1.034

fit a linear regression for central park

``` r
weather_lm = function(df) {
  lm(tmax ~ tmin, data = df)
}
```

``` r
input_list = weather_nest_df %>% pull(df)
output = vector("list", length = 3)

for (i in 1:3) {
  output[[i]] = weather_lm(input_list[[i]])
}
```
