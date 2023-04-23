Calculate Z Score in R
================
Marsja

- <a href="#example-1-calculate-z-score-for-a-vector"
  id="toc-example-1-calculate-z-score-for-a-vector"><strong>Example 1:
  Calculate Z-score for a Vector</strong></a>
- <a href="#example-2-find-z-score-for-a-matrix"
  id="toc-example-2-find-z-score-for-a-matrix"><strong>Example 2: Find
  Z-Score for a Matrix</strong></a>
- <a href="#example-3-calculate-z-score-for-a-dataframe"
  id="toc-example-3-calculate-z-score-for-a-dataframe"><strong>Example 3:
  Calculate Z-Score for a DataFrame</strong></a>
- <a href="#example-4-find-z-score-for-a-dataframe-using-mutate"
  id="toc-example-4-find-z-score-for-a-dataframe-using-mutate"><strong>Example
  4: Find Z-Score for a DataFrame using mutate()</strong></a>
- <a
  href="#example-5-calculate-z-score-for-all-numeric-variables-in-a-dataframe-using-mutate_if"
  id="toc-example-5-calculate-z-score-for-all-numeric-variables-in-a-dataframe-using-mutate_if"><strong>Example
  5: Calculate z-score for all numeric variables in a dataframe using
  mutate_if()</strong></a>
- <a href="#how-to-find-z-score-in-r-to-standardize-data"
  id="toc-how-to-find-z-score-in-r-to-standardize-data"><strong>How to
  Find Z-score in R to Standardize Data</strong></a>
- <a href="#find-the-z-score-in-r"
  id="toc-find-the-z-score-in-r"><strong>Find the Z-Score in
  R</strong></a>
- <a href="#outlier-detection" id="toc-outlier-detection">Outlier
  Detection:</a>
  - <a href="#example-data" id="toc-example-data">1. Example Data</a>
  - <a href="#find-z-score-for-your-variables"
    id="toc-find-z-score-for-your-variables"><strong>2. Find Z-Score for
    Your Variables</strong></a>
  - <a href="#use-z-scores-to-detect-possible-outliers"
    id="toc-use-z-scores-to-detect-possible-outliers"><strong>3. Use
    Z-scores to Detect Possible Outliers</strong></a>

This is the R-code for the blog post:
<https://www.marsja.se/how-to-calculate-z-score-in-r/>

## **Example 1: Calculate Z-score for a Vector**

``` r
# Generate a vector of hearing thresholds
hearing_thresholds <- c(20, 25, 30, 35, 40)

# Calculate the mean and standard deviation
mean_ht <- mean(hearing_thresholds)
sd_ht <- sd(hearing_thresholds)

# Calculate the z-score for each hearing threshold
z_scores <- (hearing_thresholds - mean_ht) / sd_ht

# Print the z-scores
print(z_scores)
```

    [1] -1.2649111 -0.6324555  0.0000000  0.6324555  1.2649111

## **Example 2: Find Z-Score for a Matrix**

``` r
# Generate a matrix of pure-tone audiometry data
pta <- matrix(c(20, 30, 40, 25, 35, 45), nrow = 2)

# Calculate the mean and standard deviation for each column
mean_pta <- apply(pta, 2, mean)
sd_pta <- apply(pta, 2, sd)

# Calculate the z-score for each value in the matrix
z_scores <- sweep(pta, 2, mean_pta, FUN = "-") / sd_pta

# Print the z-scores
print(z_scores)
```

               [,1]     [,2]       [,3]
    [1,] -0.7071068  1.06066 -0.4714045
    [2,]  0.4714045 -1.06066  0.7071068

## **Example 3: Calculate Z-Score for a DataFrame**

``` r
# Generate a dataframe of pure-tone audiometry data
df_pta <- data.frame(
  Freq = c(250, 500, 1000, 2000, 4000, 8000),
  Left = c(20, 25, 35, 50, 60, 65),
  Right = c(15, 20, 30, 40, 50, 60)
)

# Calculate the z-score for each value in the left and right columns
df_pta[, 2:3] <- scale(df_pta[, 2:3])
# Print the dataframe with z-score

print(df_pta)
```

      Freq       Left      Right
    1  250 -1.2069940 -1.1945462
    2  500 -0.9387731 -0.9078551
    3 1000 -0.4023313 -0.3344729
    4 2000  0.4023313  0.2389092
    5 4000  0.9387731  0.8122914
    6 8000  1.2069940  1.3856736

## **Example 4: Find Z-Score for a DataFrame using mutate()**

``` r
# Generate a dataframe with some hearing science data
set.seed(123)
age <- sample(18:70, 10, replace = TRUE)
gender <- sample(c("M", "F"), 10, replace = TRUE)
SSQ <- round(runif(10, min = 10, max = 100) / 10, 1)
PTA <- round(runif(10, min = 35, max = 65), 1)
df <- data.frame(age, gender, SSQ, PTA)

# Find z-scores for the SSQ and PTA columns using mutate()
library(dplyr)
df <- df %>%
  mutate(SSQ_z = (SSQ - mean(SSQ)) / sd(SSQ),
         PTA_z = (PTA - mean(PTA)) / sd(PTA))
         
# View the updated dataframe
head(df)
```

      age gender SSQ  PTA       SSQ_z       PTA_z
    1  48      F 7.2 62.1  0.25532557  1.43450653
    2  32      F 6.8 55.7  0.08510852  0.75089955
    3  68      M 9.9 58.9  1.40429064  1.09270304
    4  31      F 6.9 35.7  0.12766279 -1.38537228
    5  20      M 7.4 49.3  0.34043409  0.06729256
    6  59      F 5.9 57.8 -0.29787983  0.97520809

## **Example 5: Calculate z-score for all numeric variables in a dataframe using mutate_if()**

``` r
# Generate a dataframe with some hearing science data
set.seed(123)
age <- sample(18:70, 10, replace = TRUE)
gender <- sample(c("M", "F"), 10, replace = TRUE)
SSQ <- round(runif(10, min = 10, max = 100) / 10, 1)
PTA <- round(runif(10, min = 35, max = 65), 1)
df <- data.frame(age, gender, SSQ, PTA)

# Find z-scores for all numeric columns in the dataframe using mutate_if()
library(dplyr)
df <- df %>%
  mutate_if(is.numeric, ~ (.-mean(.)) / sd(.))
  
# View the updated dataframe
head(df)
```

             age gender         SSQ         PTA
    1 -0.1600770      F  0.25532557  1.43450653
    2 -1.0748025      F  0.08510852  0.75089955
    3  0.9833300      M  1.40429064  1.09270304
    4 -1.1319729      F  0.12766279 -1.38537228
    5 -1.7608467      M  0.34043409  0.06729256
    6  0.4687968      F -0.29787983  0.97520809

## **How to Find Z-score in R to Standardize Data**

``` r
# Set seed for reproducibility
set.seed(123)

# Generate data for SSQ with mean 7.4 and range 1-10
SSQ <- round(runif(100, min = 1, max = 100) / 10, 1)
SSQ <- round((SSQ - mean(SSQ)) / sd(SSQ) * 1.5 + 7.4, 1)

# Generate data for HHIE
hhie <- sample(1:3, 100, replace = TRUE)

# Combine data into a dataframe
df <- data.frame(SSQ, hhie)

# View the first few rows of the dataframe
head(df)
```

      SSQ hhie
    1 6.3    1
    2 8.9    2
    3 6.9    1
    4 9.4    2
    5 9.7    1
    6 5.0    3

## **Find the Z-Score in R**

``` r
# calculate z-scores for ssq and hhie
df$ssq_zscore <- (df$SSQ - mean(df$SSQ, na.rm = TRUE)) /  sd(df$SSQ, na.rm = TRUE)
df$hhie_zscore <- (df$hhie - mean(df$hhie, na.rm = TRUE)) / sd(df$hhie, na.rm = TRUE)
```

Using dplyr:

``` r
df <- df %>% 
  mutate(SSQ_zscore = (SSQ - mean(SSQ)) / sd(SSQ),
         hhie_zscore = (hhie - mean(hhie)) / sd(hhie))
```

## Outlier Detection:

### 1. Example Data

``` r
# set seed for reproducibility
set.seed(123)

# generate data for SSQ scale
ssq <- round(rnorm(100, mean = 8, sd = 1.5), 1)
# adjust scores above 10 to 10
ssq[ssq > 10] <- 10 

# generate data for HHIE scale
hhie <- round(rnorm(100, mean = 2, sd = 0.5), 0)
# adjust scores above 3 to 3
hhie[hhie > 3] <- 3 

# create data frame
data <- data.frame(
  ssq = ssq,
  hhie = hhie,
  age = round(rnorm(100, mean = 50, sd = 10)),
  gender = sample(c("male", "female"), 100, replace = TRUE),
  education = round(rnorm(100, mean = 12, sd = 2)),
  pta = round(rnorm(100, mean = 50, sd = 10))
)
```

### **2. Find Z-Score for Your Variables**

``` r
# calculate z-scores for SSQ and HHIE
data$z_ssq <- (data$ssq - mean(data$ssq)) / sd(data$ssq)
data$z_hhie <- (data$hhie - mean(data$hhie)) / sd(data$hhie)
```

  

``` r
library(dplyr)

data <- data %>%
  mutate(z_ssq = scale(ssq),
         z_hhie = scale(hhie))
```

### **3. Use Z-scores to Detect Possible Outliers**

``` r
outliers <- data[abs(data$z_ssq) > 2 | abs(data$z_hhie) > 2, ]

outliers
```

       ssq hhie age gender education pta     z_ssq    z_hhie
    18 5.1    2  43   male        10  67 -2.371536 0.1469026
    26 5.5    2  51   male        11  52 -2.053423 0.1469026
    72 4.5    2  60   male        13  49 -2.848707 0.1469026
