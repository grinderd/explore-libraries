01\_explore-libraries\_jenny.R
================
david.grinder
Wed Jan 31 17:52:58 2018

``` r
sessionInfo()
```

    ## R version 3.4.1 (2017-06-30)
    ## Platform: x86_64-w64-mingw32/x64 (64-bit)
    ## Running under: Windows 7 x64 (build 7601) Service Pack 1
    ## 
    ## Matrix products: default
    ## 
    ## locale:
    ## [1] LC_COLLATE=English_United States.1252 
    ## [2] LC_CTYPE=English_United States.1252   
    ## [3] LC_MONETARY=English_United States.1252
    ## [4] LC_NUMERIC=C                          
    ## [5] LC_TIME=English_United States.1252    
    ## 
    ## attached base packages:
    ## [1] stats     graphics  grDevices utils     datasets  methods   base     
    ## 
    ## loaded via a namespace (and not attached):
    ##  [1] compiler_3.4.1  backports_1.1.0 magrittr_1.5    rprojroot_1.2  
    ##  [5] tools_3.4.1     htmltools_0.3.6 yaml_2.1.14     Rcpp_0.12.12   
    ##  [9] stringi_1.1.5   rmarkdown_1.6   knitr_1.17      stringr_1.2.0  
    ## [13] digest_0.6.12   evaluate_0.10.1

``` r
library(fs)
```

    ## Warning: package 'fs' was built under R version 3.4.3

``` r
## how jenny might do this in a first exploration
## purposely leaving a few things to change later!
```

Which libraries does R search for packages?

``` r
.libPaths()
```

    ## [1] "C:/Users/david.grinder/Documents/R/win-library/3.4"
    ## [2] "C:/Users/david.grinder/Documents/R/R-3.4.1/library"

``` r
## let's confirm the second element is, in fact, the default library
.Library
```

    ## [1] "C:/Users/DAVID~1.GRI/DOCUME~1/R/R-34~1.1/library"

``` r
path_real(.Library)
```

    ## C:/Users/david.grinder/Documents/R/R-3.4.1/library

Installed packages

``` r
library(tidyverse)
```

    ## Warning: package 'tidyverse' was built under R version 3.4.3

    ## -- Attaching packages ---------------------------------- tidyverse 1.2.1 --

    ## v ggplot2 2.2.1     v purrr   0.2.4
    ## v tibble  1.3.4     v dplyr   0.7.4
    ## v tidyr   0.7.2     v stringr 1.2.0
    ## v readr   1.1.1     v forcats 0.2.0

    ## Warning: package 'ggplot2' was built under R version 3.4.3

    ## Warning: package 'tibble' was built under R version 3.4.2

    ## Warning: package 'tidyr' was built under R version 3.4.3

    ## Warning: package 'readr' was built under R version 3.4.3

    ## Warning: package 'purrr' was built under R version 3.4.3

    ## Warning: package 'dplyr' was built under R version 3.4.3

    ## Warning: package 'forcats' was built under R version 3.4.3

    ## -- Conflicts ------------------------------------- tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
ipt <- installed.packages() %>%
  as_tibble()

## how many packages?
nrow(ipt)
```

    ## [1] 168

Exploring the packages

``` r
## count some things! inspiration
##   * tabulate by LibPath, Priority, or both
ipt %>%
  count(LibPath, Priority)
```

    ## # A tibble: 5 x 3
    ##                                              LibPath    Priority     n
    ##                                                <chr>       <chr> <int>
    ## 1 C:/Users/david.grinder/Documents/R/R-3.4.1/library        base    14
    ## 2 C:/Users/david.grinder/Documents/R/R-3.4.1/library recommended    15
    ## 3 C:/Users/david.grinder/Documents/R/R-3.4.1/library        <NA>     1
    ## 4 C:/Users/david.grinder/Documents/R/win-library/3.4 recommended     1
    ## 5 C:/Users/david.grinder/Documents/R/win-library/3.4        <NA>   137

``` r
##   * what proportion need compilation?
ipt %>%
  count(NeedsCompilation) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 3 x 3
    ##   NeedsCompilation     n       prop
    ##              <chr> <int>      <dbl>
    ## 1               no    77 0.45833333
    ## 2              yes    83 0.49404762
    ## 3             <NA>     8 0.04761905

``` r
##   * how break down re: version of R they were built on
ipt %>%
  count(Built) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 4 x 3
    ##   Built     n        prop
    ##   <chr> <int>       <dbl>
    ## 1 3.4.0     1 0.005952381
    ## 2 3.4.1   119 0.708333333
    ## 3 3.4.2     7 0.041666667
    ## 4 3.4.3    41 0.244047619

Reflections

``` r
## reflect on ^^ and make a few notes to yourself; inspiration
##   * does the number of base + recommended packages make sense to you?
##   * how does the result of .libPaths() relate to the result of .Library?
```

Going further

``` r
## if you have time to do more ...

## is every package in .Library either base or recommended?
all_default_pkgs <- list.files(.Library)
all_br_pkgs <- ipt %>%
  filter(Priority %in% c("base", "recommended")) %>%
  pull(Package)
setdiff(all_default_pkgs, all_br_pkgs)
```

    ## [1] "translations"

``` r
## study package naming style (all lower case, contains '.', etc

## use `fields` argument to installed.packages() to get more info and use it!
ipt2 <- installed.packages(fields = "URL") %>%
  as_tibble()
ipt2 %>%
  mutate(github = grepl("github", URL)) %>%
  count(github) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 2 x 3
    ##   github     n      prop
    ##    <lgl> <int>     <dbl>
    ## 1  FALSE    87 0.5178571
    ## 2   TRUE    81 0.4821429
