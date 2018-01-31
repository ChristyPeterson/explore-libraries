01\_explore-libraries\_jenny.R
================
cpeterson
Wed Jan 31 16:05:48 2018

``` r
# Above is the yml information which is telling the compile notebook to make a github doc

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

    ## ── Attaching packages ────────────────────────────────────────── tidyverse 1.2.1 ──

    ## ✔ ggplot2 2.2.1     ✔ purrr   0.2.4
    ## ✔ tibble  1.4.2     ✔ dplyr   0.7.4
    ## ✔ tidyr   0.8.0     ✔ stringr 1.2.0
    ## ✔ readr   1.1.1     ✔ forcats 0.2.0

    ## ── Conflicts ───────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

``` r
ipt <- installed.packages() %>%
  as_tibble()

## how many packages?
nrow(ipt)
```

    ## [1] 144

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
    ## 3 /Library/Frameworks/R.framework/Versions/3.4/Resources… <NA>         115

``` r
##   * what proportion need compilation?
ipt %>%
  count(NeedsCompilation) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 3 x 3
    ##   NeedsCompilation     n   prop
    ##   <chr>            <int>  <dbl>
    ## 1 no                  64 0.444 
    ## 2 yes                 74 0.514 
    ## 3 <NA>                 6 0.0417

``` r
##   * how break down re: version of R they were built on
ipt %>%
  count(Built) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 4 x 3
    ##   Built     n   prop
    ##   <chr> <int>  <dbl>
    ## 1 3.4.0    48 0.333 
    ## 2 3.4.1    12 0.0833
    ## 3 3.4.2    27 0.188 
    ## 4 3.4.3    57 0.396

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
    ##   [5] "bindr"          "bindrcpp"       "Biobase"        "BiocGenerics"  
    ##   [9] "BiocInstaller"  "bitops"         "broom"          "callr"         
    ##  [13] "caTools"        "cellranger"     "cli"            "clipr"         
    ##  [17] "clisymbols"     "colorspace"     "crayon"         "curl"          
    ##  [21] "data.table"     "DBI"            "dbplyr"         "debugme"       
    ##  [25] "desc"           "dichromat"      "digest"         "dotCall64"     
    ##  [29] "dplyr"          "enc"            "evaluate"       "forcats"       
    ##  [33] "foreach"        "fs"             "gdata"          "ggplot2"       
    ##  [37] "gh"             "git2r"          "glmnet"         "glue"          
    ##  [41] "gplots"         "gtable"         "gtools"         "haven"         
    ##  [45] "highr"          "hms"            "htmltools"      "httpuv"        
    ##  [49] "httr"           "ini"            "iterators"      "jsonlite"      
    ##  [53] "knitr"          "labeling"       "lazyeval"       "limma"         
    ##  [57] "lubridate"      "magrittr"       "maps"           "markdown"      
    ##  [61] "matrixStats"    "metagenomeSeq"  "mime"           "mnormt"        
    ##  [65] "modelr"         "munsell"        "openssl"        "permute"       
    ##  [69] "pillar"         "pkgconfig"      "plogr"          "plyr"          
    ##  [73] "praise"         "psych"          "purrr"          "R6"            
    ##  [77] "RColorBrewer"   "Rcpp"           "readr"          "readxl"        
    ##  [81] "rematch"        "rematch2"       "reprex"         "reshape2"      
    ##  [85] "rJava"          "rlang"          "rmarkdown"      "rprojroot"     
    ##  [89] "rstudioapi"     "rvest"          "scales"         "selectr"       
    ##  [93] "shiny"          "shinydashboard" "sourcetools"    "spam"          
    ##  [97] "stringi"        "stringr"        "styler"         "testthat"      
    ## [101] "tibble"         "tidyr"          "tidyselect"     "tidyverse"     
    ## [105] "translations"   "usethis"        "utf8"           "vegan"         
    ## [109] "viridisLite"    "whisker"        "withr"          "xlsx"          
    ## [113] "xlsxjars"       "xml2"           "xtable"         "yaml"

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
    ## 1 F         71 0.493
    ## 2 T         73 0.507
