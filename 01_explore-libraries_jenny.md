01\_explore-libraries\_jenny.R
================
mb87855
Wed Jan 31 16:24:09 2018

``` r
## how jenny might do this in a first exploration
## purposely leaving a few things to change later!
```

Which libraries does R search for packages?

``` r
.libPaths()
```

    ## [1] "C:/Program Files/R/R-3.4.3/library"

``` r
## let's confirm the second element is, in fact, the default library
.Library
```

    ## [1] "C:/PROGRA~1/R/R-34~1.3/library"

``` r
library(fs)
path_real(.Library)
```

    ## C:/Program Files/R/R-3.4.3/library

Installed packages

``` r
library(tidyverse)
```

    ## -- Attaching packages ------------------------- tidyverse 1.2.1 --

    ## v ggplot2 2.2.1     v purrr   0.2.4
    ## v tibble  1.4.2     v dplyr   0.7.4
    ## v tidyr   0.7.2     v stringr 1.2.0
    ## v readr   1.1.1     v forcats 0.2.0

    ## -- Conflicts ---------------------------- tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
ipt <- installed.packages() %>%
  as_tibble()

## how many packages?
nrow(ipt)
```

    ## [1] 152

Exploring the packages

``` r
## count some things! inspiration
##   * tabulate by LibPath, Priority, or both
ipt %>%
  count(LibPath, Priority)
```

    ## # A tibble: 3 x 3
    ##   LibPath                            Priority        n
    ##   <chr>                              <chr>       <int>
    ## 1 C:/Program Files/R/R-3.4.3/library base           14
    ## 2 C:/Program Files/R/R-3.4.3/library recommended    15
    ## 3 C:/Program Files/R/R-3.4.3/library <NA>          123

``` r
##   * what proportion need compilation?
ipt %>%
  count(NeedsCompilation) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 3 x 3
    ##   NeedsCompilation     n   prop
    ##   <chr>            <int>  <dbl>
    ## 1 no                  68 0.447 
    ## 2 yes                 78 0.513 
    ## 3 <NA>                 6 0.0395

``` r
##   * how break down re: version of R they were built on
ipt %>%
  count(Built) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 3 x 3
    ##   Built     n   prop
    ##   <chr> <int>  <dbl>
    ## 1 3.4.1    13 0.0855
    ## 2 3.4.2     2 0.0132
    ## 3 3.4.3   137 0.901

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

    ##   [1] "ada"          "assertthat"   "backports"    "base64enc"   
    ##   [5] "BH"           "bindr"        "bindrcpp"     "bitops"      
    ##   [9] "broom"        "cairoDevice"  "caret"        "caTools"     
    ##  [13] "cellranger"   "cli"          "clipr"        "clisymbols"  
    ##  [17] "colorspace"   "crayon"       "curl"         "CVST"        
    ##  [21] "data.table"   "DBI"          "dbplyr"       "ddalpha"     
    ##  [25] "debugme"      "DEoptimR"     "desc"         "dichromat"   
    ##  [29] "digest"       "dimRed"       "dplyr"        "DRR"         
    ##  [33] "enc"          "evaluate"     "forcats"      "foreach"     
    ##  [37] "fs"           "gdata"        "ggplot2"      "gh"          
    ##  [41] "git2r"        "glue"         "gower"        "gplots"      
    ##  [45] "gtable"       "gtools"       "haven"        "highr"       
    ##  [49] "hms"          "htmltools"    "httr"         "ini"         
    ##  [53] "ipred"        "iterators"    "jsonlite"     "kernlab"     
    ##  [57] "knitr"        "labeling"     "lava"         "lazyeval"    
    ##  [61] "lubridate"    "magrittr"     "markdown"     "mime"        
    ##  [65] "mnormt"       "ModelMetrics" "modelr"       "munsell"     
    ##  [69] "numDeriv"     "openssl"      "pillar"       "pkgconfig"   
    ##  [73] "plogr"        "plyr"         "praise"       "prodlim"     
    ##  [77] "psych"        "purrr"        "R6"           "randomForest"
    ##  [81] "rattle"       "RColorBrewer" "Rcpp"         "RcppRoll"    
    ##  [85] "readr"        "readxl"       "recipes"      "rematch"     
    ##  [89] "rematch2"     "reprex"       "reshape2"     "RGtk2"       
    ##  [93] "rlang"        "rmarkdown"    "robustbase"   "ROCR"        
    ##  [97] "rpart.plot"   "rprojroot"    "rstudioapi"   "rvest"       
    ## [101] "scales"       "selectr"      "sfsmisc"      "SQUAREM"     
    ## [105] "stringi"      "stringr"      "styler"       "testthat"    
    ## [109] "tibble"       "tidyr"        "tidyselect"   "tidyverse"   
    ## [113] "timeDate"     "translations" "usethis"      "utf8"        
    ## [117] "viridisLite"  "whisker"      "withr"        "xgboost"     
    ## [121] "XML"          "xml2"         "yaml"

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
    ##   github     n  prop
    ##   <lgl>  <int> <dbl>
    ## 1 F         79 0.520
    ## 2 T         73 0.480
