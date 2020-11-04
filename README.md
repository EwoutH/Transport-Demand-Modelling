Multiple Linear Regression
================

**INSTITUTO SUPERIOR TECNICO, UNIVERSITY OF LISBON**  
![](https://media-exp1.licdn.com/dms/image/C560BAQG-Q7His3Mn4w/company-logo_200_200/0?e=1612396800&v=beta&t=7e2u0RwGubqElKFquvKKot2g3Y6EPjXoXDBByKZvvUs)
**TRANSPORT DEMAND MODELLING COURSE, PROF. FILIPE MOURA**  
**Prepared by [Gabriel
Valença](https://ushift.tecnico.ulisboa.pt/team-gabriel-valenca/), PhD
Candidate**

## Chicago example exercise

Trip production of 57 Traffic Assignment Zones of Chicago in 1960’s

> Your task: Estimate a linear regression model that predicts trips per
> occupied dwelling unit.

### Variables:

  - `TODU`: Motorized Trips (private car or Public Transportation) per
    occupied dwelling unit;
  - ACO: Average car ownership (cars per dwelling);
  - AHS: Average household size;
  - SRI: Social Rank Index:  
    1\. proportion of blue-collar workers (e.g., construction,
    mining);  
    2\. proportion of people with age higher than 25 years that have
    completed at least 8 year of education;  
    \> **Note:** The SRI has its maximum value when there are no
    blue-collar workers and all adults have education of at least 8
    years.

> UI: Urbanization Index: 1. fertility rate, defined as the ratio of
> children under 5 years of age to the female population of childbearing
> age;  
> 2\. female labor force participation rate, meaning the % of women who
> are in the labor force;  
> 3\. % of single family units to total dwelling units.

``` 
  ## The degree of urbanization index would be increased by
    ### a) lower fertility rate,
    ### b) higher female labor force participation rate, and
    ### c) higher proportion of single dwelling units.

  ## Note: High values for this index imply less attachment to the home
```

SI:Segregation Index \#\# It measures the proportion of an area to which
minority groups \#\# (e.g: non-whites, foreign-born, Eastern Europeans)
live in isolation.

``` 
  ## Note: High values for this index imply that those communities are less
  ## prone to leaving their living areas and as such to having lower
  ## levels of mobility.
```

### Import Libraries

Let’s begin\!

For the first time, you will need to install some of the packages. Step
by step:

1.  Go to Packages on the lower right display window and click install
2.  write the library you want to install and click “install”

Or… `install.packages("readxl","tidyverse")` etc…

``` r
library(readxl) #Library used to import excel files
library(tidyverse) # Library used in data science to perform exploratory data analysis
library(skimr) # Library used for providing a summary of the data
library(DataExplorer) # Library used in data science to perform exploratory data analysis
library(corrplot) # Library used for correlation plots
library(car) # Library used for testing autocorrelation (Durbin Watson)
library(olsrr) # Library used for testing multicollinearity (VIF, TOL, etc.)
```

### EXPLORATORY DATA ANALYSIS (EDA)

##### Import dataset

``` r
dataset <- read_excel("Data/TDM_Class3_MLR_Chicago_Example.xls") 
```

##### Check the structure of the dataset

``` r
str(dataset)
```

    ## tibble [57 x 6] (S3: tbl_df/tbl/data.frame)
    ##  $ TODU: num [1:57] 3.18 3.89 3.98 4.16 3.6 4.1 4.36 4.87 5.85 4.97 ...
    ##  $ ACO : num [1:57] 0.59 0.57 0.61 0.61 0.63 0.66 0.71 0.77 0.84 0.74 ...
    ##  $ AHS : num [1:57] 3.26 3.13 3.02 3.14 3.75 3.24 2.77 2.74 3.02 2.84 ...
    ##  $ SI  : num [1:57] 21 21.6 12.6 17.6 35.3 ...
    ##  $ SRI : num [1:57] 28.3 20.9 26 28.5 27.2 ...
    ##  $ UI  : num [1:57] 60.1 65.7 63.2 66.2 58.4 ...

##### Take a first look at the dataset

``` r
head(dataset, 10)
```

    ## # A tibble: 10 x 6
    ##     TODU   ACO   AHS    SI   SRI    UI
    ##    <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>
    ##  1  3.18 0.59   3.26 21.0   28.3  60.1
    ##  2  3.89 0.570  3.13 21.6   20.9  65.7
    ##  3  3.98 0.61   3.02 12.6   26.0  63.2
    ##  4  4.16 0.61   3.14 17.6   28.5  66.2
    ##  5  3.6  0.63   3.75 35.3   27.2  58.4
    ##  6  4.1  0.66   3.24 14.7   28.0  59.6
    ##  7  4.36 0.71   2.77 11.6   39.9  64.6
    ##  8  4.87 0.77   2.74 10.7   48.4  67.9
    ##  9  5.85 0.84   3.02  8.2   42.2  56.9
    ## 10  4.97 0.74   2.84  7.94  38.1  62.4

##### Check the type and class of the dataset

``` r
#este apenas mostra o codigo, o seguinte apenas mostra os outputs
typeof(dataset)
class(dataset)
```

    ## [1] "list"

    ## [1] "tbl_df"     "tbl"        "data.frame"

##### Transform dataset into dataframe

``` r
df <- data.frame(dataset)
```

##### Compare the structure of the dataset with df

``` r
str(dataset)
```

    ## tibble [57 x 6] (S3: tbl_df/tbl/data.frame)
    ##  $ TODU: num [1:57] 3.18 3.89 3.98 4.16 3.6 4.1 4.36 4.87 5.85 4.97 ...
    ##  $ ACO : num [1:57] 0.59 0.57 0.61 0.61 0.63 0.66 0.71 0.77 0.84 0.74 ...
    ##  $ AHS : num [1:57] 3.26 3.13 3.02 3.14 3.75 3.24 2.77 2.74 3.02 2.84 ...
    ##  $ SI  : num [1:57] 21 21.6 12.6 17.6 35.3 ...
    ##  $ SRI : num [1:57] 28.3 20.9 26 28.5 27.2 ...
    ##  $ UI  : num [1:57] 60.1 65.7 63.2 66.2 58.4 ...

``` r
str(df)
```

    ## 'data.frame':    57 obs. of  6 variables:
    ##  $ TODU: num  3.18 3.89 3.98 4.16 3.6 4.1 4.36 4.87 5.85 4.97 ...
    ##  $ ACO : num  0.59 0.57 0.61 0.61 0.63 0.66 0.71 0.77 0.84 0.74 ...
    ##  $ AHS : num  3.26 3.13 3.02 3.14 3.75 3.24 2.77 2.74 3.02 2.84 ...
    ##  $ SI  : num  21 21.6 12.6 17.6 35.3 ...
    ##  $ SRI : num  28.3 20.9 26 28.5 27.2 ...
    ##  $ UI  : num  60.1 65.7 63.2 66.2 58.4 ...

> **Note:** The dataframe function transforms columns into variables and
> rows into observations.

##### Take a look at the dataframe

``` r
head(df, 10)
```

    ##    TODU  ACO  AHS    SI   SRI    UI
    ## 1  3.18 0.59 3.26 21.01 28.32 60.10
    ## 2  3.89 0.57 3.13 21.61 20.89 65.71
    ## 3  3.98 0.61 3.02 12.57 25.99 63.19
    ## 4  4.16 0.61 3.14 17.61 28.52 66.24
    ## 5  3.60 0.63 3.75 35.32 27.18 58.36
    ## 6  4.10 0.66 3.24 14.73 27.95 59.58
    ## 7  4.36 0.71 2.77 11.61 39.91 64.64
    ## 8  4.87 0.77 2.74 10.71 48.36 67.88
    ## 9  5.85 0.84 3.02  8.20 42.15 56.86
    ## 10 4.97 0.74 2.84  7.94 38.14 62.44

##### Show summary statistics

``` r
skim(df)
```

|                                                  |      |
| :----------------------------------------------- | :--- |
| Name                                             | df   |
| Number of rows                                   | 57   |
| Number of columns                                | 6    |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |      |
| Column type frequency:                           |      |
| numeric                                          | 6    |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |      |
| Group variables                                  | None |

Data summary

**Variable type: numeric**

| skim\_variable | n\_missing | complete\_rate |  mean |    sd |    p0 |   p25 |   p50 |   p75 |  p100 | hist  |
| :------------- | ---------: | -------------: | ----: | ----: | ----: | ----: | ----: | ----: | ----: | :---- |
| TODU           |          0 |              1 |  5.37 |  1.33 |  3.02 |  4.54 |  5.10 |  6.13 |  9.14 | ▃▇▅▃▁ |
| ACO            |          0 |              1 |  0.81 |  0.18 |  0.50 |  0.67 |  0.79 |  0.92 |  1.32 | ▆▇▇▃▁ |
| AHS            |          0 |              1 |  3.19 |  0.39 |  1.83 |  3.00 |  3.19 |  3.37 |  4.50 | ▁▂▇▂▁ |
| SI             |          0 |              1 | 13.07 | 12.19 |  2.17 |  6.82 |  9.86 | 15.08 | 62.53 | ▇▂▁▁▁ |
| SRI            |          0 |              1 | 49.56 | 15.84 | 20.89 | 38.14 | 49.37 | 60.85 | 87.38 | ▅▆▇▅▂ |
| UI             |          0 |              1 | 52.62 | 13.46 | 24.08 | 44.80 | 55.51 | 61.09 | 83.66 | ▃▅▇▅▁ |

##### Check missing data

``` r
plot_missing(df)
```

![](README_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

# Create a separate bar plot for each variable, demonstrating the distributions of all continuous variables

plot\_histogram(df)

# Plot boxplots of each independent variable with TODU

plot\_boxplot(df, by = “TODU”)

# Plot correlation heatmaps

res1 \<- cor.mtest(df, conf.level = .95)

corrplot(cor(df), p.mat = res1$p, method = “number”, type = “upper”,
order=“hclust”, sig.level = 0.05)

## Note: try putting into method “color” or “circle”, and see the diference.

## Note: The pairwise correlations that are crossed are statistically insignificant.

## This means that pvalue \> 0.05, and you should not reject the null hypothesis.

## The null hypothesis is that correlation is zero.

# Therefore, take a look at this example and see for yourself:

cor.test(df\(AHS, df\)SI)

# MULTIPLE LINEAR REGRESSION

# Task: Estimate a linear regression model that predicts trips per occupied dwelling unit.

# y(TODU) = bo + b1*ACO + b2*AHS + b3*SI + b4*SRI +b5\*UI + Error

## Before running the model, you need to check if the requirements are met.

## For instance, let’s take a look if the independent variables have linear relation with the dependent variables.

plot(x = df\(TODU, y = df\)ACO, xlab = “TODU”, ylab = “ACO”) plot(x =
df\(TODU, y = df\)AHS, xlab = “TODU”, ylab = “AHS”) plot(x =
df\(TODU, y = df\)SI, xlab = “TODU”, ylab = “SI”) plot(x =
df\(TODU, y = df\)SRI, xlab = “TODU”, ylab = “SRI”) plot(x =
df\(TODU, y = df\)UI, xlab = “TODU”, ylab = “UI”)

# Or you could execute a pairwise scatterplot matrix, that compares every variable with each other:

pairs(df\[,1:6\], pch = 19, lower.panel = NULL)

# Check if the Dependent variable is normally distributed

## If sample is smaller than 2000 observations, use Shapiro-Wilk test:

shapiro.test(df$TODU)

# If not, use the Kolmogorov-Smirnov test

ks.test(df\(TODU, "pnorm", mean=mean(df\)TODU), sd = sd(df$TODU))

# Note the warning that appears in the Kolmogorov-Smirnov test:

# “ties should not be present for the Kolmogorov-Smirnov test”.

# Most likely what happened is that this test only works with continuous variables.

# Although TODU is a countinuous variable, the small sample size, makes it likely to have repeated values.

# Consequently, the test considers TODU as categorical variable. Therefore, this is another evidence,

# that for small samples it is more appropriate to use the Shapiro Test.

# Finally, let’s run the multiple linear regression model\!

model \<- lm(TODU \~ ACO + AHS + SI + SRI + UI, data = df)
summary(model) plot(model)

# Execute the Durbin Watson test to evaluate autocorrelation of the residuals

durbinWatsonTest(model)

# To calculate the VIF and TOL to test multicollinearity

ols\_vif\_tol(model)

To calculate the Condition Index to test multicollinearity
ols\_eigen\_cindex(model)

To test both simultaneously ols\_coll\_diag(model)
