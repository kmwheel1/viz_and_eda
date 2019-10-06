GG Plot 1
================
Kylie Wheelock Riley
9/26/2019

## Create weather data from NOAA

``` r
weather_df = 
  ## Dowloading wather from 3 stations
  rnoaa::meteo_pull_monitors(c("USW00094728", "USC00519397", "USS0023B17S"),
                      var = c("PRCP", "TMIN", "TMAX"), 
                      date_min = "2017-01-01",
                      date_max = "2017-12-31") %>%
  mutate(
    name = recode(id, USW00094728 = "CentralPark_NY", 
                      USC00519397 = "Waikiki_HA",
                      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) %>%
  select(name, id, everything())
```

    ## Registered S3 method overwritten by 'crul':
    ##   method                 from
    ##   as.character.form_file httr

    ## Registered S3 method overwritten by 'hoardr':
    ##   method           from
    ##   print.cache_info httr

    ## file path:          C:\Users\Kylie's Computer\AppData\Local\rnoaa\rnoaa\Cache/ghcnd/USW00094728.dly

    ## file last updated:  2019-09-26 10:26:44

    ## file min/max dates: 1869-01-01 / 2019-09-30

    ## file path:          C:\Users\Kylie's Computer\AppData\Local\rnoaa\rnoaa\Cache/ghcnd/USC00519397.dly

    ## file last updated:  2019-09-26 10:27:15

    ## file min/max dates: 1965-01-01 / 2019-09-30

    ## file path:          C:\Users\Kylie's Computer\AppData\Local\rnoaa\rnoaa\Cache/ghcnd/USS0023B17S.dly

    ## file last updated:  2019-09-26 10:27:27

    ## file min/max dates: 1999-09-01 / 2019-09-30

``` r
weather_df
```

    ## # A tibble: 1,095 x 6
    ##    name           id          date        prcp  tmax  tmin
    ##    <chr>          <chr>       <date>     <dbl> <dbl> <dbl>
    ##  1 CentralPark_NY USW00094728 2017-01-01     0   8.9   4.4
    ##  2 CentralPark_NY USW00094728 2017-01-02    53   5     2.8
    ##  3 CentralPark_NY USW00094728 2017-01-03   147   6.1   3.9
    ##  4 CentralPark_NY USW00094728 2017-01-04     0  11.1   1.1
    ##  5 CentralPark_NY USW00094728 2017-01-05     0   1.1  -2.7
    ##  6 CentralPark_NY USW00094728 2017-01-06    13   0.6  -3.8
    ##  7 CentralPark_NY USW00094728 2017-01-07    81  -3.2  -6.6
    ##  8 CentralPark_NY USW00094728 2017-01-08     0  -3.8  -8.8
    ##  9 CentralPark_NY USW00094728 2017-01-09     0  -4.9  -9.9
    ## 10 CentralPark_NY USW00094728 2017-01-10     0   7.8  -6  
    ## # ... with 1,085 more rows

## Create a gg plot

``` r
ggplot(weather_df, aes(x = tmin, y = tmax)) + 
  geom_point()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_and_eda_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

``` r
##need to add  + geom(point) to get the data to plot

##alternate way of making this plot using piping (if you need to modify data before it)
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_and_eda_files/figure-gfm/unnamed-chunk-2-2.png)<!-- -->

## Saving

``` r
## below is code to save the output and then plot it later.  Not generally the best workflow
scatterplot = 
  weather_df %>% 
  ggplot(aes(x = tmin, y = tmax))
```

## Advanced Scatterplot

``` r
##Using name as the color aestetic 
ggplot(weather_df, aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name))
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_and_eda_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

``` r
##Adding a smooth curve with geom_smooth
ggplot(weather_df, aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name), alpha = .5) +
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'gam' and formula 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).
    
    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_and_eda_files/figure-gfm/unnamed-chunk-4-2.png)<!-- -->

## Facets

``` r
##separating out variables by faceting them into their own plot
ggplot(weather_df, aes(x = tmin, y = tmax, color = name)) + 
  geom_point(alpha = .5) +
  geom_smooth(se = FALSE) + 
  facet_grid(. ~ name)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_and_eda_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

``` r
## looking at tmax by date, and points are precipitation
ggplot(weather_df, aes(x = date, y = tmax, color = name)) + 
  geom_point(aes(size = prcp), alpha = .5) +
  geom_smooth(se = FALSE) + 
  facet_grid(. ~ name)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](viz_and_eda_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

## Learning Assessment 1

Write a code chain that starts with weather\_df; focuses only on Central
Park, converts temperatures to Fahrenheit, makes a scatterplot of min
vs. max temperature, and overlays a linear regression line (using
options in geom\_smooth()).

``` r
weather_df %>% 
  filter(name == "CentralPark_NY") %>% 
  mutate(
    tmax_far = (tmax * (9/5) + 32),
    tmin_far = (tmin * (9/5) + 32)
  ) %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point() +
  geom_smooth(method = "lm", se = FALSE) 
```

![](viz_and_eda_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

``` r
## Line plot showing max temp by date for each location 
ggplot(weather_df, aes(x = date, y = tmax, color = name)) + 
  geom_smooth(se = FALSE) 
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

![](viz_and_eda_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

``` r
## More things - The geoms that you choose are independent and can be what you think are most useful. The graph above only uses geom smooth, not geom point to display the data. 
```

## 2D density

Hex is good for not showing all of the data in large datasets, avoids
overlapping

``` r
##can usegeom_hex if you install (install.packages("hexbin))
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) +
  geom_bin2d()
```

    ## Warning: Removed 15 rows containing non-finite values (stat_bin2d).

![](viz_and_eda_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

## Learning Assessment 2

In the preceding, we set the alpha aesthetic “by hand” instead of
mapping it to a variable. This is possible for other aesthetics too. To
check your understanding of this point, try to explain why the two lines
below don’t produce the same
result:

``` r
ggplot(weather_df) + geom_point(aes(x = tmax, y = tmin), color = "blue")
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_and_eda_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

``` r
ggplot(weather_df) + geom_point(aes(x = tmax, y = tmin, color = "blue"))
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_and_eda_files/figure-gfm/unnamed-chunk-10-2.png)<!-- -->

``` r
## In the second plot, color is a variables and ggplot is giving the plot a default color.  In the first you are assigning the plot the color blue. 
```

## Univariate plots

``` r
##Histogram
ggplot(weather_df, aes(x = tmax)) + 
  geom_histogram()
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 3 rows containing non-finite values (stat_bin).

![](viz_and_eda_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

``` r
##Playing around with bin width and colors - didge separates the bars so they aren't stacked 
ggplot(weather_df, aes(x = tmax, fill = name)) + 
  geom_histogram(position = "dodge", binwidth = 2)
```

    ## Warning: Removed 3 rows containing non-finite values (stat_bin).

![](viz_and_eda_files/figure-gfm/unnamed-chunk-11-2.png)<!-- -->

``` r
weather_df 
```

    ## # A tibble: 1,095 x 6
    ##    name           id          date        prcp  tmax  tmin
    ##    <chr>          <chr>       <date>     <dbl> <dbl> <dbl>
    ##  1 CentralPark_NY USW00094728 2017-01-01     0   8.9   4.4
    ##  2 CentralPark_NY USW00094728 2017-01-02    53   5     2.8
    ##  3 CentralPark_NY USW00094728 2017-01-03   147   6.1   3.9
    ##  4 CentralPark_NY USW00094728 2017-01-04     0  11.1   1.1
    ##  5 CentralPark_NY USW00094728 2017-01-05     0   1.1  -2.7
    ##  6 CentralPark_NY USW00094728 2017-01-06    13   0.6  -3.8
    ##  7 CentralPark_NY USW00094728 2017-01-07    81  -3.2  -6.6
    ##  8 CentralPark_NY USW00094728 2017-01-08     0  -3.8  -8.8
    ##  9 CentralPark_NY USW00094728 2017-01-09     0  -4.9  -9.9
    ## 10 CentralPark_NY USW00094728 2017-01-10     0   7.8  -6  
    ## # ... with 1,085 more rows

``` r
##Density plots - transparensy set to 0.4 so all values appear
ggplot(weather_df, aes(x = tmax, fill = name)) + 
  geom_density(alpha = .4, adjust = .5, color = "blue") 
```

    ## Warning: Removed 3 rows containing non-finite values (stat_density).

![](viz_and_eda_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

## Box plots

``` r
weather_df %>% 
  ggplot(aes( x = name, y = tmax)) +
  geom_boxplot()
```

    ## Warning: Removed 3 rows containing non-finite values (stat_boxplot).

![](viz_and_eda_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

## Violin plots

``` r
ggplot(weather_df, aes(x = name, y = tmax)) + 
  geom_violin(aes(fill = name), color = "blue", alpha = .5) + 
  stat_summary(fun.y = median, geom = "point", color = "blue", size = 4)
```

    ## Warning: Removed 3 rows containing non-finite values (stat_ydensity).

    ## Warning: Removed 3 rows containing non-finite values (stat_summary).

![](viz_and_eda_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->

## Ridge plots

``` r
weather_df %>% 
  ggplot(aes(x = tmax, y = name)) +
  geom_density_ridges(scale = .85)
```

    ## Picking joint bandwidth of 1.84

    ## Warning: Removed 3 rows containing non-finite values (stat_density_ridges).

![](viz_and_eda_files/figure-gfm/unnamed-chunk-15-1.png)<!-- -->

## Learning assessment 3

Make plots that compare precipitation across locations. Try a histogram,
a density plot, a boxplot, a violin plot, and a ridgeplot; use aesthetic
mappings to make your figure readable.

``` r
##Histogram
ggplot(weather_df, aes(x = prcp, fill = name)) + 
  geom_histogram(position = "dodge", binwidth = 2)
```

    ## Warning: Removed 3 rows containing non-finite values (stat_bin).

![](viz_and_eda_files/figure-gfm/unnamed-chunk-16-1.png)<!-- -->

``` r
##Density plot
ggplot(weather_df, aes(x = prcp, fill = name)) + 
  geom_density(alpha = .4, adjust = .5, color = "blue") 
```

    ## Warning: Removed 3 rows containing non-finite values (stat_density).

![](viz_and_eda_files/figure-gfm/unnamed-chunk-16-2.png)<!-- -->

``` r
##Box plot
weather_df %>% 
  ggplot(aes( x = name, y = prcp)) +
  geom_boxplot()
```

    ## Warning: Removed 3 rows containing non-finite values (stat_boxplot).

![](viz_and_eda_files/figure-gfm/unnamed-chunk-16-3.png)<!-- -->

``` r
##Violin plot
ggplot(weather_df, aes(x = name, y = prcp)) + 
  geom_violin(aes(fill = name), color = "blue", alpha = .5) + 
  stat_summary(fun.y = median, geom = "point", color = "blue", size = 4)
```

    ## Warning: Removed 3 rows containing non-finite values (stat_ydensity).

    ## Warning: Removed 3 rows containing non-finite values (stat_summary).

![](viz_and_eda_files/figure-gfm/unnamed-chunk-16-4.png)<!-- -->

``` r
#Ridge plot
weather_df %>% 
  ggplot(aes(x = prcp, y = name)) +
  geom_density_ridges(scale = 1)
```

    ## Picking joint bandwidth of 4.61

    ## Warning: Removed 3 rows containing non-finite values (stat_density_ridges).

![](viz_and_eda_files/figure-gfm/unnamed-chunk-16-5.png)<!-- -->

``` r
## want to zoom in on this one to see differences, not sure how to do that

##data is highly skewed, winner is boxplot either omitting days with zero precip, or capping precip at 100:
weather_df %>% 
  filter(prcp > 0) %>% 
  ggplot(aes(x = prcp, y = name)) + 
  geom_density_ridges(scale = .85)
```

    ## Picking joint bandwidth of 19.7

![](viz_and_eda_files/figure-gfm/unnamed-chunk-16-6.png)<!-- -->

# Saving and embedding plots

## GG Save

``` r
weather_plot = ggplot(weather_df, aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name), alpha = .5) 

ggsave("weather_plot.pdf", weather_plot, width = 8, height = 5)
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

## Embedding a plot

``` r
## Good default settings
knitr::opts_chunk$set(
  fig.width = 6,
  fig.asp = .6,
  out.width = "90%"
)
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point(alpha = .4) +
  geom_smooth(se = FALSE) 
```

    ## `geom_smooth()` using method = 'gam' and formula 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_and_eda_files/figure-gfm/unnamed-chunk-18-1.png)<!-- -->

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point(alpha = .4) +
  geom_smooth(se = FALSE) 
```

    ## `geom_smooth()` using method = 'gam' and formula 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

<img src="viz_and_eda_files/figure-gfm/unnamed-chunk-19-1.png" width="90%" />

## Learning assessment 4

Set global options for your figure sizes in the “setup” code chunk and
re-knit your document. What happens when you change fig.asp? What about
out.width?
