01\_explore-libraries\_jenny.R
================
baileraj
Wed Jan 31 14:09:10 2018

``` r
## how jenny might do this in a first exploration
## purposely leaving a few things to change later!
```

Which libraries does R search for packages?

``` r
.libPaths()
```

    ## [1] "/Library/Frameworks/R.framework/Versions/3.4/Resources/library"

``` r
## let's confirm the second element is, in fact, the default library
.Library
```

    ## [1] "/Library/Frameworks/R.framework/Resources/library"

``` r
library(fs)
path_real(.Library)
```

    ## /Library/Frameworks/R.framework/Versions/3.4/Resources/library

Installed packages

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────── tidyverse 1.2.1 ──

    ## ✔ ggplot2 2.2.1     ✔ purrr   0.2.4
    ## ✔ tibble  1.4.2     ✔ dplyr   0.7.4
    ## ✔ tidyr   0.8.0     ✔ stringr 1.2.0
    ## ✔ readr   1.1.1     ✔ forcats 0.2.0

    ## ── Conflicts ────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

``` r
ipt <- installed.packages() %>%
  as_tibble()

## how many packages?
nrow(ipt)
```

    ## [1] 147

Exploring the packages

``` r
## count some things! inspiration
##   * tabulate by LibPath, Priority, or both
ipt %>%
  count(LibPath, Priority)
```

    ## # A tibble: 3 x 3
    ##   LibPath                                                 Priority       n
    ##   <chr>                                                   <chr>      <int>
    ## 1 /Library/Frameworks/R.framework/Versions/3.4/Resources… base          14
    ## 2 /Library/Frameworks/R.framework/Versions/3.4/Resources… recommend…    15
    ## 3 /Library/Frameworks/R.framework/Versions/3.4/Resources… <NA>         118

``` r
##   * what proportion need compilation?
ipt %>%
  count(NeedsCompilation) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 3 x 3
    ##   NeedsCompilation     n   prop
    ##   <chr>            <int>  <dbl>
    ## 1 no                  65 0.442 
    ## 2 yes                 76 0.517 
    ## 3 <NA>                 6 0.0408

``` r
##   * how break down re: version of R they were built on
ipt %>%
  count(Built) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 4 x 3
    ##   Built     n  prop
    ##   <chr> <int> <dbl>
    ## 1 3.4.0    56 0.381
    ## 2 3.4.1    15 0.102
    ## 3 3.4.2    16 0.109
    ## 4 3.4.3    60 0.408

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

    ##   [1] "assertthat"     "backports"      "base64enc"      "BH"            
    ##   [5] "bindr"          "bindrcpp"       "bit"            "bit64"         
    ##   [9] "bitops"         "blob"           "broom"          "callr"         
    ##  [13] "caTools"        "cellranger"     "chron"          "cli"           
    ##  [17] "clipr"          "clisymbols"     "colorspace"     "crayon"        
    ##  [21] "curl"           "DBI"            "dbplyr"         "debugme"       
    ##  [25] "desc"           "dichromat"      "digest"         "dplyr"         
    ##  [29] "enc"            "evaluate"       "extrafont"      "extrafontdb"   
    ##  [33] "forcats"        "fs"             "gapminder"      "ggforce"       
    ##  [37] "ggplot2"        "gh"             "git2r"          "glue"          
    ##  [41] "gridExtra"      "gsubfn"         "gtable"         "haven"         
    ##  [45] "highr"          "hms"            "htmltools"      "httpuv"        
    ##  [49] "httr"           "ini"            "jsonlite"       "knitr"         
    ##  [53] "labeling"       "lazyeval"       "lmtest"         "lubridate"     
    ##  [57] "magrittr"       "mapproj"        "maps"           "markdown"      
    ##  [61] "memoise"        "mime"           "mnormt"         "modelr"        
    ##  [65] "munsell"        "openssl"        "pillar"         "pkgconfig"     
    ##  [69] "plogr"          "plyr"           "praise"         "proto"         
    ##  [73] "psych"          "purrr"          "R6"             "RColorBrewer"  
    ##  [77] "Rcpp"           "readr"          "readxl"         "rematch"       
    ##  [81] "rematch2"       "reprex"         "reshape2"       "rlang"         
    ##  [85] "rmarkdown"      "rprojroot"      "RSQLite"        "rstudioapi"    
    ##  [89] "Rttf2pt1"       "rvest"          "scales"         "selectr"       
    ##  [93] "shiny"          "shinydashboard" "sourcetools"    "sqldf"         
    ##  [97] "stringi"        "stringr"        "styler"         "testthat"      
    ## [101] "tibble"         "tidyr"          "tidyselect"     "tidyverse"     
    ## [105] "translations"   "tweenr"         "udunits2"       "units"         
    ## [109] "usethis"        "utf8"           "vcd"            "viridisLite"   
    ## [113] "waffle"         "whisker"        "withr"          "xml2"          
    ## [117] "xtable"         "yaml"           "zoo"

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
    ## 1 F         65 0.442
    ## 2 T         82 0.558
