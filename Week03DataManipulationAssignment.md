Data manipulation
=================

Introduction:
-------------

Data needs to be examined and any problems fixed before analysis can be
done.In statistics, we focus on four areas.

1.  Data accuracy
    -   make sure data types are correct
    -   check for typos
    -   check for nonsensical data
    -   check categories make sense
    -   correct problems if possible or delete
    -   reverse code items if needed
    -   score instruments if needed
    -   keep track of what you do so you can be transparent
2.  Missing data
3.  outliers
4.  Statistical assumptions

This assignment shows some more steps to take to get data ready for
analysis.

Directions:
-----------

Complete each step of the assignment below.

Package management in R
-----------------------

``` r
# keep a list of the packages used in this script
packages <- c("tidyverse","rio","jmv")
```

This next code block has eval=FALSE because you don’t want to run it
when knitting the file. Installing packages when knitting an R notebook
can be problematic.

``` r
# check each of the packages in the list and install them if they're not installed already
for (i in packages){
  if(! i %in% installed.packages()){
    install.packages(i,dependencies = TRUE)
  }
  # show each package that is checked
  print(i)
}
```

``` r
# load each package into memory so it can be used in the script
for (i in packages){
  library(i,character.only=TRUE)
  # show each package that is loaded
  print(i)
}
```

    ## -- Attaching packages --------------------------------------- tidyverse 1.3.0 --

    ## v ggplot2 3.3.3     v purrr   0.3.4
    ## v tibble  3.0.5     v dplyr   1.0.3
    ## v tidyr   1.1.2     v stringr 1.4.0
    ## v readr   1.4.0     v forcats 0.5.0

    ## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

    ## [1] "tidyverse"
    ## [1] "rio"
    ## [1] "jmv"

A note about working with packages and functions in R: When installing
packages you may see notifications that SomePackage::function() masks
OtherPackage::function(). What that means is that two different packages
used the same function name. The functions may or may not do the same
thing, but for whatever reason the pogrammers of each package happened
to choose the same name for a function in their respective package. The
last package installed will be the version of that function that will be
used if only the function name is used in your R code. The
SomePackage::function() notation with the colons in it means to use the
function named function() from the package named SomePackage. I think I
have decided when I write R code that I will always try to stick to that
notation. That way it’s very clear which package a desired function
comes from and there will be no problems with masking if different
packages used the same function name.

Open data file
--------------

The rio package works for importing several different types of data
files. We’re going to use it in this class. There are other packages
which can be used to open datasets in R. You can see several options by
clicking on the Import Dataset menu under the Environment tab in
RStudio. (For a csv file like we have this week we’d use either From
Text(base) or From Text (readr). Try it out to see the menu dialog.)

``` r
# import the Week3.rds dataset into RStudio

# Using the file.choose() command allows you to select a file to import from another folder.
# dataset <- rio::import(file.choose())

# This command will allow us to import the rds file included in our project folder.
dataset <- rio::import("Week3.rds")
```

Examine the dataset
-------------------

Examine the dataset and answer the following questions. Enter any code
you use in the following code block.

-   How many variables are in the dataset?

    -   Answer:11

-   List each variable and the level of measurement. Note if there are
    any issues you see with the data in that variable. (The first one is
    done for you.)

    -   sex - nominal, no issues noted
    -   research - nominal no issues

    +height-continuous +weight -continuous +lvst1 - ordinal
    +lvst2-ordinal +lvst3–ordinal +lvst4-ordinal +frst1-ordinal
    +frst2-ordinal +frst3-ordinal

``` r
dataset
```

Calculate a new column
----------------------

The dataset includes variables for height and weight. Calculate the BMI
for each participant by executing the following code block. The formula
for BMI is weight (kg)/height (m)^2. Round the result to one decimal
place.

-   What level of measurement is the new bmi variable?

    -   Answer:

``` r
dataset <- dataset %>% mutate(bmi = round(weight/(height/100)^2,1)) 
```

``` r
dataset
```

Reverse code a column
---------------------

The dataset contains questions for two fictitious instruments
(questionnaires): 1) the Love of Statistics Scale, and 2) the Fear of
Statistics Scale.

The Love of Statistics Scale consists of 4 questions rated on a scale of
1-5. Before the instrument can be scored, the last question needs to be
reverse coded. Execute the following code block to reverse code the last
question in the Love of Statistics Scale.

-   What level of measurement is the new lvst4reverse variable?

    -   Answer: level of measurement is the same just reversed. ordinal,
        putting things in a rank

``` r
# The line below is how I first reverse coded the variable. It seemed to work, but I later found some problems with the z-scores.
# While the text values in the variable did get changed, the order of the factor levels also got reversed numerically.
# The result was that the number values really didn't change. I left this here to make you aware to check results are what you expect when you modify the variables.
# It's easy to miss a problem like this in your code.
#dataset <- dataset %>% mutate(lvst4reverse = recode(lvst4, "completely"="Not at all", "mostly"="a little", "moderately"="moderately", "a little"="mostly", "Not at all"="completely"))

# The line below correctly reverse coded the lvst4 variable.
dataset <- dataset %>% mutate(lvst4reverse = 6 - as.numeric(lvst4))
```

``` r
dataset
```

Score the Love of Statistics Scale instrument
---------------------------------------------

Run the following code block to calculate a final score for the Love of
Statistics Scale for each participant.

-   What level of measurement is the new LOSS\_total

    -   Answer:

``` r
dataset <- dataset %>% mutate(LOSS_total = as.numeric(lvst1) + as.numeric(lvst2) + as.numeric(lvst3) + as.numeric(lvst4reverse))
```

Score the Fear of Statistics Scale instrument
---------------------------------------------

Enter code in the following code block to calcuate a final score for the
Fear of Statistics Scale for each participant. Call the new variable
FOSS\_total.

``` r
# Enter code here below the comment.
dataset <- dataset %>% mutate(FOSS_total = as.numeric(frst1) + as.numeric(frst2) + as.numeric(frst3))
```

Examine the new LOSS\_total and FOSS\_total variables
-----------------------------------------------------

Note any issues with the data in the new variables. Place code you use
to examine the variables in the code block below. (Or just describe what
you did.)

``` r
dataset
```

    ##       sex research height weight      lvst1      lvst2      lvst3      lvst4
    ## 1  Female     Qual  147.1   46.7 moderately moderately     mostly moderately
    ## 2  Female     Qual  145.8   63.0 moderately   a little     mostly     mostly
    ## 3    Male    Quant  152.1   57.8   a little   a little   a little moderately
    ## 4    Male    Quant  181.3   84.3   a little   a little moderately moderately
    ## 5  Female    Quant  143.0   52.4   a little   a little moderately   a little
    ## 6    Male    Quant  139.5   44.3 moderately moderately moderately     mostly
    ## 7    Male    Quant  174.6   69.8 moderately moderately     mostly   a little
    ## 8    Male     Qual  198.7   98.4 moderately   a little moderately moderately
    ## 9    Male     Qual  175.9   84.7 moderately moderately     mostly moderately
    ## 10 Female    Quant  139.5   52.0   a little   a little     mostly moderately
    ## 11   Male     Qual  172.6   84.3 moderately   a little moderately moderately
    ## 12 Female     Qual  138.9   37.6 moderately moderately moderately moderately
    ## 13 Female     Qual  187.1   83.6     mostly     mostly     mostly moderately
    ## 14   Male    Quant  179.2   82.1   a little moderately moderately moderately
    ## 15   Male    Quant  156.4   75.2   a little moderately moderately   a little
    ## 16 Female    Quant  153.3   49.6   a little   a little moderately     mostly
    ## 17   Male     Qual  194.2  103.8 moderately moderately moderately moderately
    ## 18   Male     Qual  177.9   47.1 moderately Not at all Not at all completely
    ## 19   Male     Qual  159.6   72.8   a little   a little   a little moderately
    ## 20   Male    Quant  148.3   59.2 moderately   a little moderately     mostly
    ## 21 Female     Qual  152.0   65.7 completely moderately completely moderately
    ## 22   Male     Qual  136.1   42.8   a little   a little   a little   a little
    ## 23   Male     Qual  156.0   69.1     mostly   a little     mostly     mostly
    ## 24 Female     Qual  164.5   51.0     mostly   a little moderately     mostly
    ## 25 Female     Qual  144.7   45.7     mostly     mostly moderately moderately
    ## 26   Male     Qual  183.2   82.5     mostly moderately moderately     mostly
    ## 27   Male    Quant  134.8   70.3   a little   a little moderately moderately
    ## 28 Female     Qual  134.9   51.5 Not at all     mostly moderately Not at all
    ## 29 Female     Qual  133.3   36.3     mostly     mostly completely   a little
    ## 30 Female     Qual  158.5   52.2 completely moderately completely     mostly
    ## 31   Male     Qual  162.0   62.6   a little moderately moderately   a little
    ## 32 Female    Quant  131.3   64.2   a little   a little     mostly   a little
    ## 33   Male    Quant  163.5   65.3 moderately     mostly     mostly moderately
    ## 34 Female     Qual  146.4   57.1 moderately moderately completely   a little
    ## 35 Female     Qual  151.3   60.1 moderately     mostly     mostly   a little
    ## 36   Male     Qual  173.6   84.7 moderately moderately     mostly moderately
    ## 37   Male     Qual  191.3   94.7     mostly   a little moderately     mostly
    ## 38 Female    Quant  171.4   54.8   a little   a little     mostly moderately
    ## 39   Male    Quant  164.6   59.3 Not at all Not at all Not at all moderately
    ## 40 Female    Quant  142.3   54.5 Not at all   a little   a little moderately
    ## 41   Male     Qual  164.4   63.8 moderately moderately     mostly moderately
    ## 42 Female     Qual  154.3   61.7 moderately moderately moderately moderately
    ## 43   Male     Qual  180.9  102.4 completely     mostly     mostly moderately
    ## 44   Male     Qual  170.2   72.4 moderately moderately moderately     mostly
    ## 45 Female     Qual  161.3   58.4 moderately moderately completely   a little
    ## 46   Male    Quant  162.3   60.6   a little   a little moderately moderately
    ## 47   Male    Quant  176.2   91.8 moderately Not at all moderately     mostly
    ## 48   Male    Quant  183.9   86.7   a little   a little Not at all     mostly
    ## 49   Male    Quant  126.6   49.9   a little   a little   a little moderately
    ## 50   Male     Qual  171.2   57.4 completely Not at all   a little completely
    ## 51 Female     Qual  160.2   81.4   a little moderately moderately   a little
    ## 52 Female     Qual  143.7   56.0 moderately   a little moderately moderately
    ## 53 Female     Qual  145.7   57.2 moderately     mostly     mostly   a little
    ## 54 Female    Quant  159.8   73.5 moderately     mostly     mostly moderately
    ## 55 Female     Qual  153.6   66.3 moderately moderately completely   a little
    ## 56 Female    Quant  172.1   58.5   a little moderately moderately moderately
    ## 57 Female     Qual  151.9   50.2   a little Not at all moderately moderately
    ## 58   Male    Quant  164.2   84.1 moderately     mostly moderately moderately
    ## 59 Female     Qual  128.6   65.1   a little moderately moderately   a little
    ## 60 Female    Quant  184.2   95.8 moderately moderately     mostly moderately
    ## 61 Female    Quant  156.7   72.3   a little     mostly completely Not at all
    ## 62 Female     Qual  187.5   96.9 completely     mostly completely moderately
    ## 63 Female     Qual  141.3   42.5     mostly completely completely   a little
    ## 64   Male    Quant  168.9   79.4 moderately moderately     mostly moderately
    ## 65   Male    Quant  159.7   67.7     mostly   a little moderately     mostly
    ## 66 Female    Quant  181.1   80.7 moderately   a little   a little     mostly
    ## 67   Male    Quant  154.8   65.2   a little Not at all Not at all     mostly
    ## 68 Female     Qual  156.4   64.9     mostly moderately     mostly moderately
    ## 69 Female     Qual  130.1   39.2     mostly moderately completely   a little
    ## 70   Male    Quant  184.9   86.9 moderately moderately     mostly moderately
    ## 71   Male    Quant  190.6   91.5 moderately moderately moderately moderately
    ## 72   Male    Quant  182.7   88.0 moderately moderately     mostly moderately
    ## 73 Female    Quant  144.2   73.1 moderately moderately completely   a little
    ## 74   Male    Quant  180.4   77.1   a little Not at all   a little     mostly
    ## 75 Female     Qual  150.2   78.5 moderately   a little     mostly moderately
    ## 76 Female    Quant  198.3   87.3   a little     mostly     mostly   a little
    ## 77   Male    Quant  151.6   54.1   a little moderately moderately moderately
    ## 78 Female    Quant  120.5   48.4   a little Not at all moderately moderately
    ## 79   Male     Qual  164.4   83.5 moderately Not at all   a little     mostly
    ## 80   Male    Quant  159.2   77.2 Not at all moderately moderately   a little
    ## 81 Female     Qual  151.4   67.7     mostly moderately     mostly moderately
    ## 82 Female     Qual  178.9   79.5 moderately     mostly completely   a little
    ## 83 Female    Quant  186.2   95.7   a little   a little moderately   a little
    ## 84 Female     Qual  159.5   57.3 moderately moderately     mostly moderately
    ## 85 Female     Qual  135.4   46.1 moderately moderately     mostly moderately
    ## 86   Male    Quant  183.1   69.8   a little moderately moderately     mostly
    ## 87 Female     Qual  184.9   95.9 moderately   a little     mostly moderately
    ## 88   Male    Quant  179.3   79.7 moderately moderately     mostly moderately
    ## 89 Female    Quant  121.1    9.2 moderately   a little moderately     mostly
    ## 90   Male    Quant  172.2   82.6     mostly moderately completely moderately
    ## 91 Female    Quant  140.3   58.1     mostly moderately     mostly     mostly
    ## 92   Male    Quant  117.4   38.1 moderately   a little   a little     mostly
    ## 93   Male     Qual  169.9   60.1   a little   a little moderately moderately
    ## 94   Male     Qual  187.2   98.2 Not at all moderately   a little   a little
    ## 95 Female     Qual  150.0   54.5     mostly   a little completely moderately
    ## 96   Male    Quant  178.7   80.6 moderately   a little moderately moderately
    ##         frst1      frst2      frst3  bmi lvst4reverse FOSS_total
    ## 1  moderately   a little   a little 21.6            3          7
    ## 2  moderately   a little moderately 29.6            2          8
    ## 3      mostly moderately     mostly 25.0            3         11
    ## 4      mostly moderately     mostly 25.6            3         11
    ## 5  moderately   a little     mostly 25.6            4          9
    ## 6      mostly moderately moderately 22.8            2         10
    ## 7  moderately Not at all   a little 22.9            4          6
    ## 8      mostly   a little     mostly 24.9            3         10
    ## 9  moderately Not at all   a little 27.4            3          6
    ## 10 moderately moderately     mostly 26.7            3         10
    ## 11     mostly Not at all moderately 28.3            3          8
    ## 12 moderately Not at all     mostly 19.5            3          8
    ## 13   a little Not at all   a little 23.9            3          5
    ## 14     mostly moderately     mostly 25.6            3         11
    ## 15   a little   a little moderately 30.7            4          7
    ## 16 completely moderately completely 21.1            2         13
    ## 17   a little   a little moderately 27.5            3          7
    ## 18     mostly completely completely 14.9            1         14
    ## 19 moderately moderately     mostly 28.6            3         10
    ## 20     mostly   a little     mostly 26.9            2         10
    ## 21   a little Not at all   a little 28.4            3          5
    ## 22 moderately Not at all moderately 23.1            4          7
    ## 23 moderately   a little     mostly 28.4            2          9
    ## 24     mostly moderately     mostly 18.8            2         11
    ## 25   a little Not at all   a little 21.8            3          5
    ## 26 moderately   a little     mostly 24.6            2          9
    ## 27   a little   a little moderately 38.7            3          7
    ## 28 moderately Not at all   a little 28.3            5          6
    ## 29   a little Not at all   a little 20.4            4          5
    ## 30 moderately Not at all     mostly 20.8            2          8
    ## 31 moderately   a little moderately 23.9            4          8
    ## 32 moderately Not at all moderately 37.2            4          7
    ## 33 moderately moderately moderately 24.4            3          9
    ## 34   a little Not at all   a little 26.6            4          5
    ## 35   a little Not at all   a little 26.3            4          5
    ## 36 moderately Not at all moderately 28.1            3          7
    ## 37     mostly   a little completely 25.9            2         11
    ## 38 completely   a little completely 18.7            3         12
    ## 39     mostly     mostly completely 21.9            3         13
    ## 40 moderately moderately     mostly 26.9            3         10
    ## 41 moderately   a little   a little 23.6            3          7
    ## 42 moderately   a little moderately 25.9            3          8
    ## 43 Not at all Not at all Not at all 31.3            3          3
    ## 44     mostly   a little     mostly 25.0            2         10
    ## 45 moderately Not at all moderately 22.4            4          7
    ## 46     mostly moderately     mostly 23.0            3         11
    ## 47     mostly     mostly completely 29.6            2         13
    ## 48 moderately moderately     mostly 25.6            2         10
    ## 49 moderately   a little   a little 31.1            3          7
    ## 50 moderately moderately     mostly 19.6            1         10
    ## 51   a little moderately moderately 31.7            4          8
    ## 52     mostly   a little     mostly 27.1            3         10
    ## 53   a little Not at all   a little 26.9            4          5
    ## 54 moderately Not at all moderately 28.8            3          7
    ## 55 moderately Not at all Not at all 28.1            4          5
    ## 56 moderately   a little moderately 19.8            3          8
    ## 57     mostly     mostly     mostly 21.8            3         12
    ## 58   a little   a little   a little 31.2            3          6
    ## 59 moderately Not at all moderately 39.4            4          7
    ## 60   a little   a little moderately 28.2            3          7
    ## 61 moderately Not at all moderately 29.4            5          7
    ## 62   a little Not at all   a little 27.6            3          5
    ## 63 Not at all Not at all Not at all 21.3            4          3
    ## 64 moderately   a little moderately 27.8            3          8
    ## 65 moderately   a little moderately 26.5            2          8
    ## 66 moderately   a little     mostly 24.6            2          9
    ## 67 moderately moderately completely 27.2            2         11
    ## 68   a little   a little   a little 26.5            3          6
    ## 69   a little Not at all Not at all 23.2            4          4
    ## 70 moderately   a little     mostly 25.4            3          9
    ## 71 moderately   a little moderately 25.2            3          8
    ## 72 moderately   a little moderately 26.4            3          8
    ## 73   a little   a little moderately 35.2            4          7
    ## 74 completely     mostly completely 23.7            2         14
    ## 75 moderately   a little     mostly 34.8            3          9
    ## 76   a little   a little   a little 22.2            4          6
    ## 77     mostly moderately     mostly 23.5            3         11
    ## 78 moderately   a little moderately 33.3            3          8
    ## 79     mostly   a little completely 30.9            2         11
    ## 80 moderately moderately     mostly 30.5            4         10
    ## 81   a little   a little   a little 29.5            3          6
    ## 82   a little Not at all Not at all 24.8            4          4
    ## 83 moderately   a little completely 27.6            4         10
    ## 84 moderately   a little moderately 22.5            3          8
    ## 85   a little   a little moderately 25.1            3          7
    ## 86 moderately     mostly     mostly 20.8            2         11
    ## 87     mostly Not at all completely 28.1            3         10
    ## 88   a little moderately   a little 24.8            3          7
    ## 89     mostly moderately moderately  6.3            2         10
    ## 90   a little moderately Not at all 27.9            3          6
    ## 91   a little   a little moderately 29.5            2          7
    ## 92 moderately   a little   a little 27.6            2          7
    ## 93     mostly   a little moderately 20.8            3          9
    ## 94 moderately   a little     mostly 28.0            4          9
    ## 95   a little   a little   a little 24.2            3          6
    ## 96 moderately   a little moderately 25.2            3          8

Final scores using averages
---------------------------

Not all instruments are scored using sums. If all items are measured
using the same scale a mean can be calculated. One advantage of using
means is the final score is in the same scale as the original items so
interpreting can be a bit more straightforward, It’s also possible to
calculate the mean when there are missing data without changing the
scale of the final score. Run the following code block to score the
instruments using means instead of sums.

``` r
dataset <- dataset %>% mutate(LOSS_mean = (as.numeric(lvst1) + as.numeric(lvst2) + as.numeric(lvst3) + as.numeric(lvst4reverse))/4)
dataset <- dataset %>% mutate(FOSS_mean = (as.numeric(frst1) + as.numeric(frst2) + as.numeric(frst3))/3)
```

z-scores
--------

Z-scores are useful for a number of reasons. Since z-scores are unit
free (the units are standard deviations) they can be used to compare
scores between instruments or tests which have different units. (Like
our LOSS scale which is in love units and our FOSS scale which is in
fear units.) Also, z-scores let us see how many standard deviations a
score is away from the mean. It is an easy way to see which scores are
potenential outliers. Z-scores above 2 are more than 2 standard
deviations away from the mean and are potential univariate outliers.Run
the following code block to calculate z-scores for the LOSS and FOSS
variables. Examine the new variables.

-   How do the z-scores compare between the sum scores and the mean
    scores?

    -   Answer:

-   Are there any z-scores greater than 2 in the LOSS and FOSS
    variables? How many?

    -   LOSS z-score \> 2:
    -   FOSS z-score \> 2:

-   What does it mean if a z-score is positive or negative?

    -   positive:
    -   negative:

``` r
dataset
```

    ##       sex research height weight      lvst1      lvst2      lvst3      lvst4
    ## 1  Female     Qual  147.1   46.7 moderately moderately     mostly moderately
    ## 2  Female     Qual  145.8   63.0 moderately   a little     mostly     mostly
    ## 3    Male    Quant  152.1   57.8   a little   a little   a little moderately
    ## 4    Male    Quant  181.3   84.3   a little   a little moderately moderately
    ## 5  Female    Quant  143.0   52.4   a little   a little moderately   a little
    ## 6    Male    Quant  139.5   44.3 moderately moderately moderately     mostly
    ## 7    Male    Quant  174.6   69.8 moderately moderately     mostly   a little
    ## 8    Male     Qual  198.7   98.4 moderately   a little moderately moderately
    ## 9    Male     Qual  175.9   84.7 moderately moderately     mostly moderately
    ## 10 Female    Quant  139.5   52.0   a little   a little     mostly moderately
    ## 11   Male     Qual  172.6   84.3 moderately   a little moderately moderately
    ## 12 Female     Qual  138.9   37.6 moderately moderately moderately moderately
    ## 13 Female     Qual  187.1   83.6     mostly     mostly     mostly moderately
    ## 14   Male    Quant  179.2   82.1   a little moderately moderately moderately
    ## 15   Male    Quant  156.4   75.2   a little moderately moderately   a little
    ## 16 Female    Quant  153.3   49.6   a little   a little moderately     mostly
    ## 17   Male     Qual  194.2  103.8 moderately moderately moderately moderately
    ## 18   Male     Qual  177.9   47.1 moderately Not at all Not at all completely
    ## 19   Male     Qual  159.6   72.8   a little   a little   a little moderately
    ## 20   Male    Quant  148.3   59.2 moderately   a little moderately     mostly
    ## 21 Female     Qual  152.0   65.7 completely moderately completely moderately
    ## 22   Male     Qual  136.1   42.8   a little   a little   a little   a little
    ## 23   Male     Qual  156.0   69.1     mostly   a little     mostly     mostly
    ## 24 Female     Qual  164.5   51.0     mostly   a little moderately     mostly
    ## 25 Female     Qual  144.7   45.7     mostly     mostly moderately moderately
    ## 26   Male     Qual  183.2   82.5     mostly moderately moderately     mostly
    ## 27   Male    Quant  134.8   70.3   a little   a little moderately moderately
    ## 28 Female     Qual  134.9   51.5 Not at all     mostly moderately Not at all
    ## 29 Female     Qual  133.3   36.3     mostly     mostly completely   a little
    ## 30 Female     Qual  158.5   52.2 completely moderately completely     mostly
    ## 31   Male     Qual  162.0   62.6   a little moderately moderately   a little
    ## 32 Female    Quant  131.3   64.2   a little   a little     mostly   a little
    ## 33   Male    Quant  163.5   65.3 moderately     mostly     mostly moderately
    ## 34 Female     Qual  146.4   57.1 moderately moderately completely   a little
    ## 35 Female     Qual  151.3   60.1 moderately     mostly     mostly   a little
    ## 36   Male     Qual  173.6   84.7 moderately moderately     mostly moderately
    ## 37   Male     Qual  191.3   94.7     mostly   a little moderately     mostly
    ## 38 Female    Quant  171.4   54.8   a little   a little     mostly moderately
    ## 39   Male    Quant  164.6   59.3 Not at all Not at all Not at all moderately
    ## 40 Female    Quant  142.3   54.5 Not at all   a little   a little moderately
    ## 41   Male     Qual  164.4   63.8 moderately moderately     mostly moderately
    ## 42 Female     Qual  154.3   61.7 moderately moderately moderately moderately
    ## 43   Male     Qual  180.9  102.4 completely     mostly     mostly moderately
    ## 44   Male     Qual  170.2   72.4 moderately moderately moderately     mostly
    ## 45 Female     Qual  161.3   58.4 moderately moderately completely   a little
    ## 46   Male    Quant  162.3   60.6   a little   a little moderately moderately
    ## 47   Male    Quant  176.2   91.8 moderately Not at all moderately     mostly
    ## 48   Male    Quant  183.9   86.7   a little   a little Not at all     mostly
    ## 49   Male    Quant  126.6   49.9   a little   a little   a little moderately
    ## 50   Male     Qual  171.2   57.4 completely Not at all   a little completely
    ## 51 Female     Qual  160.2   81.4   a little moderately moderately   a little
    ## 52 Female     Qual  143.7   56.0 moderately   a little moderately moderately
    ## 53 Female     Qual  145.7   57.2 moderately     mostly     mostly   a little
    ## 54 Female    Quant  159.8   73.5 moderately     mostly     mostly moderately
    ## 55 Female     Qual  153.6   66.3 moderately moderately completely   a little
    ## 56 Female    Quant  172.1   58.5   a little moderately moderately moderately
    ## 57 Female     Qual  151.9   50.2   a little Not at all moderately moderately
    ## 58   Male    Quant  164.2   84.1 moderately     mostly moderately moderately
    ## 59 Female     Qual  128.6   65.1   a little moderately moderately   a little
    ## 60 Female    Quant  184.2   95.8 moderately moderately     mostly moderately
    ## 61 Female    Quant  156.7   72.3   a little     mostly completely Not at all
    ## 62 Female     Qual  187.5   96.9 completely     mostly completely moderately
    ## 63 Female     Qual  141.3   42.5     mostly completely completely   a little
    ## 64   Male    Quant  168.9   79.4 moderately moderately     mostly moderately
    ## 65   Male    Quant  159.7   67.7     mostly   a little moderately     mostly
    ## 66 Female    Quant  181.1   80.7 moderately   a little   a little     mostly
    ## 67   Male    Quant  154.8   65.2   a little Not at all Not at all     mostly
    ## 68 Female     Qual  156.4   64.9     mostly moderately     mostly moderately
    ## 69 Female     Qual  130.1   39.2     mostly moderately completely   a little
    ## 70   Male    Quant  184.9   86.9 moderately moderately     mostly moderately
    ## 71   Male    Quant  190.6   91.5 moderately moderately moderately moderately
    ## 72   Male    Quant  182.7   88.0 moderately moderately     mostly moderately
    ## 73 Female    Quant  144.2   73.1 moderately moderately completely   a little
    ## 74   Male    Quant  180.4   77.1   a little Not at all   a little     mostly
    ## 75 Female     Qual  150.2   78.5 moderately   a little     mostly moderately
    ## 76 Female    Quant  198.3   87.3   a little     mostly     mostly   a little
    ## 77   Male    Quant  151.6   54.1   a little moderately moderately moderately
    ## 78 Female    Quant  120.5   48.4   a little Not at all moderately moderately
    ## 79   Male     Qual  164.4   83.5 moderately Not at all   a little     mostly
    ## 80   Male    Quant  159.2   77.2 Not at all moderately moderately   a little
    ## 81 Female     Qual  151.4   67.7     mostly moderately     mostly moderately
    ## 82 Female     Qual  178.9   79.5 moderately     mostly completely   a little
    ## 83 Female    Quant  186.2   95.7   a little   a little moderately   a little
    ## 84 Female     Qual  159.5   57.3 moderately moderately     mostly moderately
    ## 85 Female     Qual  135.4   46.1 moderately moderately     mostly moderately
    ## 86   Male    Quant  183.1   69.8   a little moderately moderately     mostly
    ## 87 Female     Qual  184.9   95.9 moderately   a little     mostly moderately
    ## 88   Male    Quant  179.3   79.7 moderately moderately     mostly moderately
    ## 89 Female    Quant  121.1    9.2 moderately   a little moderately     mostly
    ## 90   Male    Quant  172.2   82.6     mostly moderately completely moderately
    ## 91 Female    Quant  140.3   58.1     mostly moderately     mostly     mostly
    ## 92   Male    Quant  117.4   38.1 moderately   a little   a little     mostly
    ## 93   Male     Qual  169.9   60.1   a little   a little moderately moderately
    ## 94   Male     Qual  187.2   98.2 Not at all moderately   a little   a little
    ## 95 Female     Qual  150.0   54.5     mostly   a little completely moderately
    ## 96   Male    Quant  178.7   80.6 moderately   a little moderately moderately
    ##         frst1      frst2      frst3  bmi lvst4reverse FOSS_total LOSS_mean
    ## 1  moderately   a little   a little 21.6            3          7      3.25
    ## 2  moderately   a little moderately 29.6            2          8      2.75
    ## 3      mostly moderately     mostly 25.0            3         11      2.25
    ## 4      mostly moderately     mostly 25.6            3         11      2.50
    ## 5  moderately   a little     mostly 25.6            4          9      2.75
    ## 6      mostly moderately moderately 22.8            2         10      2.75
    ## 7  moderately Not at all   a little 22.9            4          6      3.50
    ## 8      mostly   a little     mostly 24.9            3         10      2.75
    ## 9  moderately Not at all   a little 27.4            3          6      3.25
    ## 10 moderately moderately     mostly 26.7            3         10      2.75
    ## 11     mostly Not at all moderately 28.3            3          8      2.75
    ## 12 moderately Not at all     mostly 19.5            3          8      3.00
    ## 13   a little Not at all   a little 23.9            3          5      3.75
    ## 14     mostly moderately     mostly 25.6            3         11      2.75
    ## 15   a little   a little moderately 30.7            4          7      3.00
    ## 16 completely moderately completely 21.1            2         13      2.25
    ## 17   a little   a little moderately 27.5            3          7      3.00
    ## 18     mostly completely completely 14.9            1         14      1.50
    ## 19 moderately moderately     mostly 28.6            3         10      2.25
    ## 20     mostly   a little     mostly 26.9            2         10      2.50
    ## 21   a little Not at all   a little 28.4            3          5      4.00
    ## 22 moderately Not at all moderately 23.1            4          7      2.50
    ## 23 moderately   a little     mostly 28.4            2          9      3.00
    ## 24     mostly moderately     mostly 18.8            2         11      2.75
    ## 25   a little Not at all   a little 21.8            3          5      3.50
    ## 26 moderately   a little     mostly 24.6            2          9      3.00
    ## 27   a little   a little moderately 38.7            3          7      2.50
    ## 28 moderately Not at all   a little 28.3            5          6      3.25
    ## 29   a little Not at all   a little 20.4            4          5      4.25
    ## 30 moderately Not at all     mostly 20.8            2          8      3.75
    ## 31 moderately   a little moderately 23.9            4          8      3.00
    ## 32 moderately Not at all moderately 37.2            4          7      3.00
    ## 33 moderately moderately moderately 24.4            3          9      3.50
    ## 34   a little Not at all   a little 26.6            4          5      3.75
    ## 35   a little Not at all   a little 26.3            4          5      3.75
    ## 36 moderately Not at all moderately 28.1            3          7      3.25
    ## 37     mostly   a little completely 25.9            2         11      2.75
    ## 38 completely   a little completely 18.7            3         12      2.75
    ## 39     mostly     mostly completely 21.9            3         13      1.50
    ## 40 moderately moderately     mostly 26.9            3         10      2.00
    ## 41 moderately   a little   a little 23.6            3          7      3.25
    ## 42 moderately   a little moderately 25.9            3          8      3.00
    ## 43 Not at all Not at all Not at all 31.3            3          3      4.00
    ## 44     mostly   a little     mostly 25.0            2         10      2.75
    ## 45 moderately Not at all moderately 22.4            4          7      3.75
    ## 46     mostly moderately     mostly 23.0            3         11      2.50
    ## 47     mostly     mostly completely 29.6            2         13      2.25
    ## 48 moderately moderately     mostly 25.6            2         10      1.75
    ## 49 moderately   a little   a little 31.1            3          7      2.25
    ## 50 moderately moderately     mostly 19.6            1         10      2.25
    ## 51   a little moderately moderately 31.7            4          8      3.00
    ## 52     mostly   a little     mostly 27.1            3         10      2.75
    ## 53   a little Not at all   a little 26.9            4          5      3.75
    ## 54 moderately Not at all moderately 28.8            3          7      3.50
    ## 55 moderately Not at all Not at all 28.1            4          5      3.75
    ## 56 moderately   a little moderately 19.8            3          8      2.75
    ## 57     mostly     mostly     mostly 21.8            3         12      2.25
    ## 58   a little   a little   a little 31.2            3          6      3.25
    ## 59 moderately Not at all moderately 39.4            4          7      3.00
    ## 60   a little   a little moderately 28.2            3          7      3.25
    ## 61 moderately Not at all moderately 29.4            5          7      4.00
    ## 62   a little Not at all   a little 27.6            3          5      4.25
    ## 63 Not at all Not at all Not at all 21.3            4          3      4.50
    ## 64 moderately   a little moderately 27.8            3          8      3.25
    ## 65 moderately   a little moderately 26.5            2          8      2.75
    ## 66 moderately   a little     mostly 24.6            2          9      2.25
    ## 67 moderately moderately completely 27.2            2         11      1.50
    ## 68   a little   a little   a little 26.5            3          6      3.50
    ## 69   a little Not at all Not at all 23.2            4          4      4.00
    ## 70 moderately   a little     mostly 25.4            3          9      3.25
    ## 71 moderately   a little moderately 25.2            3          8      3.00
    ## 72 moderately   a little moderately 26.4            3          8      3.25
    ## 73   a little   a little moderately 35.2            4          7      3.75
    ## 74 completely     mostly completely 23.7            2         14      1.75
    ## 75 moderately   a little     mostly 34.8            3          9      3.00
    ## 76   a little   a little   a little 22.2            4          6      3.50
    ## 77     mostly moderately     mostly 23.5            3         11      2.75
    ## 78 moderately   a little moderately 33.3            3          8      2.25
    ## 79     mostly   a little completely 30.9            2         11      2.00
    ## 80 moderately moderately     mostly 30.5            4         10      2.75
    ## 81   a little   a little   a little 29.5            3          6      3.50
    ## 82   a little Not at all Not at all 24.8            4          4      4.00
    ## 83 moderately   a little completely 27.6            4         10      2.75
    ## 84 moderately   a little moderately 22.5            3          8      3.25
    ## 85   a little   a little moderately 25.1            3          7      3.25
    ## 86 moderately     mostly     mostly 20.8            2         11      2.50
    ## 87     mostly Not at all completely 28.1            3         10      3.00
    ## 88   a little moderately   a little 24.8            3          7      3.25
    ## 89     mostly moderately moderately  6.3            2         10      2.50
    ## 90   a little moderately Not at all 27.9            3          6      3.75
    ## 91   a little   a little moderately 29.5            2          7      3.25
    ## 92 moderately   a little   a little 27.6            2          7      2.25
    ## 93     mostly   a little moderately 20.8            3          9      2.50
    ## 94 moderately   a little     mostly 28.0            4          9      2.50
    ## 95   a little   a little   a little 24.2            3          6      3.50
    ## 96 moderately   a little moderately 25.2            3          8      2.75
    ##    FOSS_mean
    ## 1   2.333333
    ## 2   2.666667
    ## 3   3.666667
    ## 4   3.666667
    ## 5   3.000000
    ## 6   3.333333
    ## 7   2.000000
    ## 8   3.333333
    ## 9   2.000000
    ## 10  3.333333
    ## 11  2.666667
    ## 12  2.666667
    ## 13  1.666667
    ## 14  3.666667
    ## 15  2.333333
    ## 16  4.333333
    ## 17  2.333333
    ## 18  4.666667
    ## 19  3.333333
    ## 20  3.333333
    ## 21  1.666667
    ## 22  2.333333
    ## 23  3.000000
    ## 24  3.666667
    ## 25  1.666667
    ## 26  3.000000
    ## 27  2.333333
    ## 28  2.000000
    ## 29  1.666667
    ## 30  2.666667
    ## 31  2.666667
    ## 32  2.333333
    ## 33  3.000000
    ## 34  1.666667
    ## 35  1.666667
    ## 36  2.333333
    ## 37  3.666667
    ## 38  4.000000
    ## 39  4.333333
    ## 40  3.333333
    ## 41  2.333333
    ## 42  2.666667
    ## 43  1.000000
    ## 44  3.333333
    ## 45  2.333333
    ## 46  3.666667
    ## 47  4.333333
    ## 48  3.333333
    ## 49  2.333333
    ## 50  3.333333
    ## 51  2.666667
    ## 52  3.333333
    ## 53  1.666667
    ## 54  2.333333
    ## 55  1.666667
    ## 56  2.666667
    ## 57  4.000000
    ## 58  2.000000
    ## 59  2.333333
    ## 60  2.333333
    ## 61  2.333333
    ## 62  1.666667
    ## 63  1.000000
    ## 64  2.666667
    ## 65  2.666667
    ## 66  3.000000
    ## 67  3.666667
    ## 68  2.000000
    ## 69  1.333333
    ## 70  3.000000
    ## 71  2.666667
    ## 72  2.666667
    ## 73  2.333333
    ## 74  4.666667
    ## 75  3.000000
    ## 76  2.000000
    ## 77  3.666667
    ## 78  2.666667
    ## 79  3.666667
    ## 80  3.333333
    ## 81  2.000000
    ## 82  1.333333
    ## 83  3.333333
    ## 84  2.666667
    ## 85  2.333333
    ## 86  3.666667
    ## 87  3.333333
    ## 88  2.333333
    ## 89  3.333333
    ## 90  2.000000
    ## 91  2.333333
    ## 92  2.333333
    ## 93  3.000000
    ## 94  3.000000
    ## 95  2.000000
    ## 96  2.666667

``` r
dataset
```

Save your data for next week
----------------------------

``` r
rio::export(dataset,"Week4.rds")
```

Save your output.
-----------------

Save this assignment as a markdown file.

Submit your assignment.
-----------------------

Submit the output you just saved for your assignment.
