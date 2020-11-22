Final Project Draft
================
Xiaoyu Li

``` r
library(tidyverse)
```

    ## -- Attaching packages --------------------------------------- tidyverse 1.3.0 --

    ## v ggplot2 3.3.2     v purrr   0.3.4
    ## v tibble  3.0.3     v dplyr   1.0.2
    ## v tidyr   1.1.2     v stringr 1.4.0
    ## v readr   1.3.1     v forcats 0.5.0

    ## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(readxl)
library(patchwork)

knitr::opts_chunk$set(
  fig.width = 8,
  fig.asp = .6,
  fig.height = 8,
  out.width = "90%"
)

theme_set(theme_minimal() + theme(legend.position = "bottom"))

options(
  ggplot2.continuous.colour = "viridis",
  ggplot2.continuous.fill = "viridis"
)

scale_colour_discrete = scale_colour_viridis_d
scale_fill_discrete = scale_fill_viridis_d
```

## Import the crime data

``` r
crime_df = 
  read_xls(
  "./data/seven-major-felony-offenses-by-precinct-2000-2019.xls",
  range = "B3:V619") %>% 
  filter(CRIME == "TOTAL SEVEN MAJOR FELONY OFFENSES")

pct_df =
  read_xls(
  "./data/seven-major-felony-offenses-by-precinct-2000-2019.xls",
  range = "A3:A619") %>% 
  drop_na(PCT)


total_crime_df =
  crime_df %>% 
  mutate(PCT = pct_df$PCT) %>% 
  select(PCT, "2013":"2019") %>% 
  pivot_longer("2013":"2019", names_to = "year", values_to = "total_crime") %>%
  mutate(borough = case_when(
    PCT <= 34 ~ "Manhattan",
    between(PCT, 40, 52) ~ "Bronx",
    between(PCT, 60, 94) ~ "Brooklyn",
    between(PCT, 100, 115) ~ "Queens",
    between(PCT, 120, 123) ~ "Staten Island"
  )) %>%
  group_by(borough, year) %>% 
  summarize(total_crime = sum(total_crime))
```

    ## `summarise()` regrouping output by 'borough' (override with `.groups` argument)
