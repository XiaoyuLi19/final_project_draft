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

``` r
write.csv(total_crime_df, "./data/total_crime.csv")
```

*“total\_crime” is the total number of 7 major felonies*

Import census data

``` r
census_df = 
  read_csv("./data/Census_Demographics_at_the_Neighborhood_Tabulation_Area__NTA__level.csv") %>% 
  select("Geographic Area - Borough", "Total Population 2010 Number") %>% 
  rename(borough = "Geographic Area - Borough",
         population_2010 = "Total Population 2010 Number"
  ) %>% 
  group_by(borough) %>% 
  summarize(population_2010 = sum(population_2010)) %>% 
  drop_na(population_2010)
```

    ## Parsed with column specification:
    ## cols(
    ##   `Geographic Area - Borough` = col_character(),
    ##   `Geographic Area - 2010 Census FIPS County Code` = col_double(),
    ##   `Geographic Area - Neighborhood Tabulation Area (NTA)* Code` = col_character(),
    ##   `Geographic Area - Neighborhood Tabulation Area (NTA)* Name` = col_character(),
    ##   `Total Population 2000 Number` = col_double(),
    ##   `Total Population 2010 Number` = col_double(),
    ##   `Total Population Change 2000-2010 Number` = col_double(),
    ##   `Total Population Change 2000-2010 Percent` = col_double()
    ## )

    ## `summarise()` ungrouping output (override with `.groups` argument)

``` r
write.csv(census_df, "./data/census.csv")
```

``` r
urlfile = "https://raw.githubusercontent.com/yw3436/p8105_final.github.io/main/data/hiv_complete.csv"

hiv_df = 
  read.csv(url(urlfile))
```
