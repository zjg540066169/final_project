visualization
================
Keyi Wang
11/8/2019

## reading data

``` r
ufo_data = read_csv("tidied_data_final.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   latitude = col_double(),
    ##   longitude = col_double(),
    ##   date_time = col_character(),
    ##   city_description = col_character(),
    ##   ufo_shape = col_character(),
    ##   encounter_length = col_double(),
    ##   described_encounter_length = col_character(),
    ##   description = col_character(),
    ##   date_documented = col_character(),
    ##   country = col_character(),
    ##   state = col_character(),
    ##   city = col_character()
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
## A bargraph showing the total number of UFO observed among 7 countries.

ufo %>%
  group_by(country) %>%
  count() %>%
  ungroup()%>%
 mutate(precentage = n/66516,
      country = factor(country),
      country = forcats::fct_reorder(country, n))%>%
ggplot(aes(x = country, y = precentage , fill = country, label =  scales::percent(precentage))) + 
    geom_col(position = 'dodge') + 
    geom_text(position = position_dodge(width = .9),  
              vjust = -0.5,   size = 3) + 
    labs(title = "Bargraph of the precentage of UFO Observed by Country",
         x = "Country",y = "Precentage") 
```

<img src="visualization_-Keyi_files/figure-gfm/unnamed-chunk-2-1.png" width="90%" />
Comment: According to the graph above, we can see that most of the data
come from the United Statesa and some of them come from Canada.
Australia and the United Kingdom contributed very litter amount of
data.

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
Comment: In this plot, we can see that for the United States the
earliest data happens before 1925 and sometime after 1960 the amount of
UFO sighting begins to increase. We can also see that most of UFO
sighting reports taken place around 1990 and after. Similar trend for
Canada, but the amount of them is not as much as the United States.

``` r
### Boxplot showing the distribution of the UFO Shape Observed in US
  
ufo %>%
  filter(country == "USA",year >= 1950) %>%
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
Comment: According to this plot, we can see that the light UFO shape was
sighted most often, and the changed shape was the least often according
to their medium value. Some of the most common sighted UFO shapes are
light, trianle, circle, unknown, other, and fireball, disk, and sphere.
Some of the least common sighted UFO shapes are changed crescent, delta,
flare, hezagon, pyramid and round.

``` r
## heatmap showing 
  ufo %>%
  filter(country == "USA", year >= 1950, encounter_length <6000) %>%
  select(year, month, encounter_length) %>%
  mutate(year = as.numeric(year),
         month = as.numeric(month)) %>%
  group_by(year,month) %>%
  arrange(desc(month)) %>%
  mutate(
  ave_encountered_length = mean(encounter_length/60)) %>%
  select(-encounter_length) %>%
  ungroup() %>%
  mutate(month = month.name[as.numeric(month)]) %>%
  ggplot(aes(x = year, y = month)) +
  geom_tile(aes(fill = ave_encountered_length), colour = "white") +
  scale_fill_gradient(low = "lightyellow", high = "darkblue") +
  scale_x_continuous(name="Years", 
                     breaks = c(1950,    1955,1960,1965,1970,1975,1980,1985,1990,1995,2000,2005,2010,2015
                                ), limits= c (1950,2015))+
  labs(
    title = "Average UFO Encounter Length Compared among Months Across Time in USA ",
    x = "Years",
    y = "Month"
  ) + 
  theme(axis.text.x = element_text(angle = 90)) +
  theme(plot.subtitle = element_text(hjust = 0.5))
```

<img src="visualization_-Keyi_files/figure-gfm/unnamed-chunk-5-1.png" width="90%" />

``` r
### Canada heatmap
 ufo %>%
  filter(country == "Canada", year >= 1950, encounter_length < 6000) %>%
  select(year, month, encounter_length) %>%
  arrange(year) %>%
  group_by(year, month)%>%
  mutate(
    encounter_length = encounter_length/60,
  ave_encountered_length = mean(encounter_length))%>%
  select(-encounter_length) %>%
  arrange(desc(month)) %>%
  ggplot(aes(x = year, y = month)) +
  geom_tile(aes(fill = ave_encountered_length), colour = "white") +
  scale_fill_gradient(low = "lightyellow", high = "darkgreen") 
```

<img src="visualization_-Keyi_files/figure-gfm/unnamed-chunk-5-2.png" width="90%" />
