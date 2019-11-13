visualization
================
Keyi Wang
11/8/2019

## reading data

``` r
ufo_data = readr::read_csv("https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2019/2019-06-25/ufo_sightings.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   date_time = col_character(),
    ##   city_area = col_character(),
    ##   state = col_character(),
    ##   country = col_character(),
    ##   ufo_shape = col_character(),
    ##   encounter_length = col_double(),
    ##   described_encounter_length = col_character(),
    ##   description = col_character(),
    ##   date_documented = col_character(),
    ##   latitude = col_double(),
    ##   longitude = col_double()
    ## )

``` r
ufo =
ufo_data %>%  
na.omit(ufo_data) %>% 
separate(date_time, into = c( "date","time"), sep = " " ) %>%
separate( date, into = c("month","day","year"), sep = "/") %>% 
mutate(country = recode(country , 
                        "us" = "the United State", 
                        "au" = "Australia",
                        "gb" = "the United Kingdom",
                        "ca" = "Canada")) 
```

``` r
## A bargraph showing the total number of UFO observed among 4 countries. As the number of UFO  observed in Australia and Britain is really low, for later graphs, we decided to omit data in these two countries.

ufo %>%
  group_by(country) %>%
  count() %>%
 mutate(precentage = n/66516) %>%
ggplot(aes(x = country, y = precentage , fill = country, label =  scales::percent(precentage))) + 
    geom_col(position = 'dodge') + 
    geom_text(position = position_dodge(width = .9),  
              vjust = -0.5,   size = 3) + 
    labs(title = "Bargraph of the precentage of UFO Observed by Country",
         x = "Country",y = "Precentage") 
```

<img src="visualization_-Keyi_files/figure-gfm/unnamed-chunk-2-1.png" width="90%" />

``` r
## line plot showing the number of UFO observed across time among countries

ufo %>%
group_by(year) %>%
count(country) %>%
ungroup() %>%
mutate(year = as.numeric(year)) %>%
  ggplot(aes(x = year,y = n,group = country)) +
  geom_line(aes(color = country)) +
  theme(axis.text.x = element_text(angle = 90)) +
  labs(
title = " Line Plot of Number of UFO Observed Across Time Among different Countries ",
    x = "Year",
    y = "Number Of UFO Observed"
  )
```

<img src="visualization_-Keyi_files/figure-gfm/unnamed-chunk-3-1.png" width="90%" />

``` r
### Boxplot showing the distribution of the UFO Shape Observed in US
  
ufo %>%
  filter(country == "the United State")%>%
  group_by(state) %>%
  count(ufo_shape) %>%
  arrange(desc(n)) %>% 
  ungroup %>% 
  mutate(
  ufo_shape = factor(ufo_shape),
  ufo_shape = forcats::fct_reorder(ufo_shape, n)) %>%
  ggplot(aes(x = ufo_shape, y = n, group = ufo_shape)) +
  geom_boxplot(aes(color =ufo_shape)) +
  theme(axis.text.x = element_text(angle = 300)) +
  labs(
    title = "Boxplot showing distribution of the UFO Shape Observed in US ",
    x = "UFO Shape",
    y = "Number"
  )
```

<img src="visualization_-Keyi_files/figure-gfm/unnamed-chunk-4-1.png" width="90%" />