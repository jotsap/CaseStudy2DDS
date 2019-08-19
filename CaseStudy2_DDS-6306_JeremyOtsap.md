---
title: "DDS Case Study 2"
author: "Jeremy Otsap"
date: "August 11, 2019"
output: 
  html_document:
    keep_md: true
---



## Initial Data Cleaning

First we load the data into a data frame. With over 36 parameters, the immediate goal is to determine which ones contribute any insight. 

### Removing Non-contributing Parameters

*Database UniqueID Values*
ID and EmployeeNumber do not provide actual data, just unique object identifiers, and are thus candidate for elimination from our analysis

*Uni-valued Parameters*
Additionaly several other parameters are candidates for removal from full analysis:

* All rows have "Y" for Over18
* All rows have 1 for EmployeeCount
* All rows have 80 for StandardHours

We create a new data frame with these 4 parameters removed



```r
# loading data
employee.df <- read.csv(
  "https://raw.githubusercontent.com/jotsap/CaseStudy2DDS/master/data/CaseStudy2-data.csv", 
  sep = ",", 
  header = TRUE)
str(employee.df)
```

```
## 'data.frame':	870 obs. of  36 variables:
##  $ ID                      : int  1 2 3 4 5 6 7 8 9 10 ...
##  $ Age                     : int  32 40 35 32 24 27 41 37 34 34 ...
##  $ Attrition               : Factor w/ 2 levels "No","Yes": 1 1 1 1 1 1 1 1 1 1 ...
##  $ BusinessTravel          : Factor w/ 3 levels "Non-Travel","Travel_Frequently",..: 3 3 2 3 2 2 3 3 3 2 ...
##  $ DailyRate               : int  117 1308 200 801 567 294 1283 309 1333 653 ...
##  $ Department              : Factor w/ 3 levels "Human Resources",..: 3 2 2 3 2 2 2 3 3 2 ...
##  $ DistanceFromHome        : int  13 14 18 1 2 10 5 10 10 10 ...
##  $ Education               : int  4 3 2 4 1 2 5 4 4 4 ...
##  $ EducationField          : Factor w/ 6 levels "Human Resources",..: 2 4 2 3 6 2 4 2 2 6 ...
##  $ EmployeeCount           : int  1 1 1 1 1 1 1 1 1 1 ...
##  $ EmployeeNumber          : int  859 1128 1412 2016 1646 733 1448 1105 1055 1597 ...
##  $ EnvironmentSatisfaction : int  2 3 3 3 1 4 2 4 3 4 ...
##  $ Gender                  : Factor w/ 2 levels "Female","Male": 2 2 2 1 1 2 2 1 1 2 ...
##  $ HourlyRate              : int  73 44 60 48 32 32 90 88 87 92 ...
##  $ JobInvolvement          : int  3 2 3 3 3 3 4 2 3 2 ...
##  $ JobLevel                : int  2 5 3 3 1 3 1 2 1 2 ...
##  $ JobRole                 : Factor w/ 9 levels "Healthcare Representative",..: 8 6 5 8 7 5 7 8 9 1 ...
##  $ JobSatisfaction         : int  4 3 4 4 4 1 3 4 3 3 ...
##  $ MaritalStatus           : Factor w/ 3 levels "Divorced","Married",..: 1 3 3 2 3 1 2 1 2 2 ...
##  $ MonthlyIncome           : int  4403 19626 9362 10422 3760 8793 2127 6694 2220 5063 ...
##  $ MonthlyRate             : int  9250 17544 19944 24032 17218 4809 5561 24223 18410 15332 ...
##  $ NumCompaniesWorked      : int  2 1 2 1 1 1 2 2 1 1 ...
##  $ Over18                  : Factor w/ 1 level "Y": 1 1 1 1 1 1 1 1 1 1 ...
##  $ OverTime                : Factor w/ 2 levels "No","Yes": 1 1 1 1 2 1 2 2 2 1 ...
##  $ PercentSalaryHike       : int  11 14 11 19 13 21 12 14 19 14 ...
##  $ PerformanceRating       : int  3 3 3 3 3 4 3 3 3 3 ...
##  $ RelationshipSatisfaction: int  3 1 3 3 3 3 1 3 4 2 ...
##  $ StandardHours           : int  80 80 80 80 80 80 80 80 80 80 ...
##  $ StockOptionLevel        : int  1 0 0 2 0 2 0 3 1 1 ...
##  $ TotalWorkingYears       : int  8 21 10 14 6 9 7 8 1 8 ...
##  $ TrainingTimesLastYear   : int  3 2 2 3 2 4 5 5 2 3 ...
##  $ WorkLifeBalance         : int  2 4 3 3 3 2 2 3 3 2 ...
##  $ YearsAtCompany          : int  5 20 2 14 6 9 4 1 1 8 ...
##  $ YearsInCurrentRole      : int  2 7 2 10 3 7 2 0 1 2 ...
##  $ YearsSinceLastPromotion : int  0 4 2 5 1 1 0 0 0 7 ...
##  $ YearsWithCurrManager    : int  3 9 2 7 3 7 3 0 0 7 ...
```

```r
head(employee.df) 
```

```
##   ID Age Attrition    BusinessTravel DailyRate             Department
## 1  1  32        No     Travel_Rarely       117                  Sales
## 2  2  40        No     Travel_Rarely      1308 Research & Development
## 3  3  35        No Travel_Frequently       200 Research & Development
## 4  4  32        No     Travel_Rarely       801                  Sales
## 5  5  24        No Travel_Frequently       567 Research & Development
## 6  6  27        No Travel_Frequently       294 Research & Development
##   DistanceFromHome Education   EducationField EmployeeCount EmployeeNumber
## 1               13         4    Life Sciences             1            859
## 2               14         3          Medical             1           1128
## 3               18         2    Life Sciences             1           1412
## 4                1         4        Marketing             1           2016
## 5                2         1 Technical Degree             1           1646
## 6               10         2    Life Sciences             1            733
##   EnvironmentSatisfaction Gender HourlyRate JobInvolvement JobLevel
## 1                       2   Male         73              3        2
## 2                       3   Male         44              2        5
## 3                       3   Male         60              3        3
## 4                       3 Female         48              3        3
## 5                       1 Female         32              3        1
## 6                       4   Male         32              3        3
##                  JobRole JobSatisfaction MaritalStatus MonthlyIncome
## 1        Sales Executive               4      Divorced          4403
## 2      Research Director               3        Single         19626
## 3 Manufacturing Director               4        Single          9362
## 4        Sales Executive               4       Married         10422
## 5     Research Scientist               4        Single          3760
## 6 Manufacturing Director               1      Divorced          8793
##   MonthlyRate NumCompaniesWorked Over18 OverTime PercentSalaryHike
## 1        9250                  2      Y       No                11
## 2       17544                  1      Y       No                14
## 3       19944                  2      Y       No                11
## 4       24032                  1      Y       No                19
## 5       17218                  1      Y      Yes                13
## 6        4809                  1      Y       No                21
##   PerformanceRating RelationshipSatisfaction StandardHours
## 1                 3                        3            80
## 2                 3                        1            80
## 3                 3                        3            80
## 4                 3                        3            80
## 5                 3                        3            80
## 6                 4                        3            80
##   StockOptionLevel TotalWorkingYears TrainingTimesLastYear WorkLifeBalance
## 1                1                 8                     3               2
## 2                0                21                     2               4
## 3                0                10                     2               3
## 4                2                14                     3               3
## 5                0                 6                     2               3
## 6                2                 9                     4               2
##   YearsAtCompany YearsInCurrentRole YearsSinceLastPromotion
## 1              5                  2                       0
## 2             20                  7                       4
## 3              2                  2                       2
## 4             14                 10                       5
## 5              6                  3                       1
## 6              9                  7                       1
##   YearsWithCurrManager
## 1                    3
## 2                    9
## 3                    2
## 4                    7
## 5                    3
## 6                    7
```

```r
# No employees have "N" for 'Over18' parameter
# Thus candidate for elimination
employee.df[employee.df$Over18 != "Y",]
```

```
##  [1] ID                       Age                     
##  [3] Attrition                BusinessTravel          
##  [5] DailyRate                Department              
##  [7] DistanceFromHome         Education               
##  [9] EducationField           EmployeeCount           
## [11] EmployeeNumber           EnvironmentSatisfaction 
## [13] Gender                   HourlyRate              
## [15] JobInvolvement           JobLevel                
## [17] JobRole                  JobSatisfaction         
## [19] MaritalStatus            MonthlyIncome           
## [21] MonthlyRate              NumCompaniesWorked      
## [23] Over18                   OverTime                
## [25] PercentSalaryHike        PerformanceRating       
## [27] RelationshipSatisfaction StandardHours           
## [29] StockOptionLevel         TotalWorkingYears       
## [31] TrainingTimesLastYear    WorkLifeBalance         
## [33] YearsAtCompany           YearsInCurrentRole      
## [35] YearsSinceLastPromotion  YearsWithCurrManager    
## <0 rows> (or 0-length row.names)
```

```r
# No employees have a value other than 1
# Thus candidate for elimination
employee.df[employee.df$EmployeeCount != 1,]
```

```
##  [1] ID                       Age                     
##  [3] Attrition                BusinessTravel          
##  [5] DailyRate                Department              
##  [7] DistanceFromHome         Education               
##  [9] EducationField           EmployeeCount           
## [11] EmployeeNumber           EnvironmentSatisfaction 
## [13] Gender                   HourlyRate              
## [15] JobInvolvement           JobLevel                
## [17] JobRole                  JobSatisfaction         
## [19] MaritalStatus            MonthlyIncome           
## [21] MonthlyRate              NumCompaniesWorked      
## [23] Over18                   OverTime                
## [25] PercentSalaryHike        PerformanceRating       
## [27] RelationshipSatisfaction StandardHours           
## [29] StockOptionLevel         TotalWorkingYears       
## [31] TrainingTimesLastYear    WorkLifeBalance         
## [33] YearsAtCompany           YearsInCurrentRole      
## [35] YearsSinceLastPromotion  YearsWithCurrManager    
## <0 rows> (or 0-length row.names)
```

```r
# Every employee had '80' for StandardHours
# Thus candidate for elimination
employee.df[employee.df$StandardHours != 80,]
```

```
##  [1] ID                       Age                     
##  [3] Attrition                BusinessTravel          
##  [5] DailyRate                Department              
##  [7] DistanceFromHome         Education               
##  [9] EducationField           EmployeeCount           
## [11] EmployeeNumber           EnvironmentSatisfaction 
## [13] Gender                   HourlyRate              
## [15] JobInvolvement           JobLevel                
## [17] JobRole                  JobSatisfaction         
## [19] MaritalStatus            MonthlyIncome           
## [21] MonthlyRate              NumCompaniesWorked      
## [23] Over18                   OverTime                
## [25] PercentSalaryHike        PerformanceRating       
## [27] RelationshipSatisfaction StandardHours           
## [29] StockOptionLevel         TotalWorkingYears       
## [31] TrainingTimesLastYear    WorkLifeBalance         
## [33] YearsAtCompany           YearsInCurrentRole      
## [35] YearsSinceLastPromotion  YearsWithCurrManager    
## <0 rows> (or 0-length row.names)
```

```r
# Placing remaining variables into data frame
emp_clean.df <- employee.df %>% dplyr::select(
  Age,
  Attrition,
  BusinessTravel,
  DailyRate,
  Department,
  DistanceFromHome,
  Education,
  EducationField,
  EnvironmentSatisfaction,
  Gender,
  HourlyRate,
  JobInvolvement,
  JobLevel,
  JobRole,
  JobSatisfaction,
  MaritalStatus,
  MonthlyIncome,
  MonthlyRate,
  NumCompaniesWorked,
  OverTime,
  PercentSalaryHike,
  PerformanceRating,
  RelationshipSatisfaction,
  StockOptionLevel,
  TotalWorkingYears,
  TrainingTimesLastYear,
  WorkLifeBalance,
  YearsAtCompany,
  YearsInCurrentRole,
  YearsSinceLastPromotion,
  YearsWithCurrManager
)

# Checking for missing or "na" values
# none found
table(emp_clean.df %>% is.na())
```

```
## 
## FALSE 
## 26970
```


### Problem Variables

We have a number of other problematic parameters that we need to address before going into a deeper analysis. 

*Multiple Values for Salary and Rate*
One of the response variables we are trying to predict is salary, based on the MonthlyIncome values. However, there are two other parameters regarding money for which we have no explanation as to their meaning:

* HourlyRate
* DailyRate
* MonthlyRate

Given that we have no idea what these represent, we need to understand if there is evidence of a relationship with either response MonthlyIncome or Attrition. 

A visual analysis of each of HourlyRate, DailyRate, and MonthlyRate show a uniform distribution of all values. Which differs significantly from the right-skewed distribution for Salary, which also has several outliers on the upper bound. [Note also typical of most samples of Salary]



```r
# histogram of MonthlyRate
hist(employee.df$MonthlyIncome, main = "Monthly Income")
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-2-1.png)<!-- -->

```r
# histogram of MonthlyRate
hist(employee.df$MonthlyRate, main = "Monthly Rate")
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-2-2.png)<!-- -->

```r
# histogram of HourlyRate
hist(employee.df$HourlyRate, main = "Hourly Rate")
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-2-3.png)<!-- -->

```r
# histogram of DailyRate
hist(employee.df$DailyRate, main = "Daily Rate")
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-2-4.png)<!-- -->

When testing for correlation among these parameters, there appears to be no evidence of a correlation to MonthlyIncome vs any of the other three. Note its concerning that additionaly there is no evidence of correlation of DailyRate, HourlyRate, or MonthlyRate to each other. At the very least one should expect to see an employee's Hourly Rate or Daily Rate have some relation to each other, for instance if these were employees in a consultancy.


```r
# No evidence of correlation between MonthlyRate and HourlyRate
# R value of -0.01603517 
cor.test(employee.df$MonthlyRate, employee.df$HourlyRate)
```

```
## 
## 	Pearson's product-moment correlation
## 
## data:  employee.df$MonthlyRate and employee.df$HourlyRate
## t = -0.47249, df = 868, p-value = 0.6367
## alternative hypothesis: true correlation is not equal to 0
## 95 percent confidence interval:
##  -0.08241306  0.05048437
## sample estimates:
##         cor 
## -0.01603517
```

```r
# No evidence of correlation between DailyRate and HourlyRate
# R value of 0.04849597 
cor.test(employee.df$DailyRate, employee.df$HourlyRate)
```

```
## 
## 	Pearson's product-moment correlation
## 
## data:  employee.df$DailyRate and employee.df$HourlyRate
## t = 1.4305, df = 868, p-value = 0.1529
## alternative hypothesis: true correlation is not equal to 0
## 95 percent confidence interval:
##  -0.01802787  0.11459234
## sample estimates:
##        cor 
## 0.04849597
```

```r
# No evidence of correlation between MonthlyRate and DailyRate
# R value of -0.02764077 
cor.test(employee.df$MonthlyRate, employee.df$DailyRate)
```

```
## 
## 	Pearson's product-moment correlation
## 
## data:  employee.df$MonthlyRate and employee.df$DailyRate
## t = -0.81466, df = 868, p-value = 0.4155
## alternative hypothesis: true correlation is not equal to 0
## 95 percent confidence interval:
##  -0.09393393  0.03889642
## sample estimates:
##         cor 
## -0.02764077
```

```r
# No evidence of correlation between MonthlyRate and MonthlyIncome
# R value of 0.06459407 
cor.test(employee.df$MonthlyRate, employee.df$MonthlyIncome)
```

```
## 
## 	Pearson's product-moment correlation
## 
## data:  employee.df$MonthlyRate and employee.df$MonthlyIncome
## t = 1.907, df = 868, p-value = 0.05684
## alternative hypothesis: true correlation is not equal to 0
## 95 percent confidence interval:
##  -0.001879735  0.130499526
## sample estimates:
##        cor 
## 0.06459407
```

```r
# No evidence of correlation between MonthlyIncome and HourlyRate
# R value of 0.002391151 
cor.test(employee.df$MonthlyIncome, employee.df$HourlyRate)
```

```
## 
## 	Pearson's product-moment correlation
## 
## data:  employee.df$MonthlyIncome and employee.df$HourlyRate
## t = 0.070448, df = 868, p-value = 0.9439
## alternative hypothesis: true correlation is not equal to 0
## 95 percent confidence interval:
##  -0.06408477  0.06884594
## sample estimates:
##         cor 
## 0.002391151
```

```r
# No evidence of correlation between MonthlyIncome and DailyRate
# R value of 0.00008790339 
cor.test(employee.df$MonthlyIncome, employee.df$DailyRate)
```

```
## 
## 	Pearson's product-moment correlation
## 
## data:  employee.df$MonthlyIncome and employee.df$DailyRate
## t = 0.0025898, df = 868, p-value = 0.9979
## alternative hypothesis: true correlation is not equal to 0
## 95 percent confidence interval:
##  -0.06637822  0.06655325
## sample estimates:
##          cor 
## 8.790339e-05
```

When looking at their relationship with Attrition, no clear distinction emerges for HourlyRate, DailyRate, or MonthlyRate. However for MonthlyIncome the distribution visually appears different between the "yes" and "no" groups


```r
# Attrition vs HourlyRate
boxplot(employee.df$HourlyRate ~ employee.df$Attrition, 
        ylab = "Dollars",
        main = "Hourly Rate by Attrition")
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

```r
# Attrition vs DailyRate
boxplot(employee.df$DailyRate ~ employee.df$Attrition, 
        ylab = "Dollars",
        main = "Hourly Rate by Attrition")
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-4-2.png)<!-- -->

```r
# Attrition vs MonthlyRate
boxplot(employee.df$MonthlyRate ~ employee.df$Attrition, 
        ylab = "Dollars",
        main = "Monthly Rate by Attrition")
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-4-3.png)<!-- -->

```r
# Attrition vs MonthlyIncome
boxplot(employee.df$MonthlyIncome ~ employee.df$Attrition, 
        ylab = "Dollars",
        main = "Monthly Income by Attrition")
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-4-4.png)<!-- -->


Lastly, when we do a scatter plot for all combinations of these parameters, no visual patterns emerge.  


```r
# Pairwise scatterplot
pairs(~ MonthlyIncome + HourlyRate + DailyRate + MonthlyRate,
  data = employee.df,
  main = "Scatterplot for All Monetary Parameters")
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

To clarify, all three of these values allude to some sort of salary. We are are told the *only* value of the three that we do know is MonthlyIncome. HourlyRate, DailyRate and MonthlyRate have no explanation, and if they were related to something similar, such as how much an employee's billing rate is [i.e. for consultancy], then there should have been a correlation between HourlyRate and DailyRate. Which leads to the larger concern of what do these variables mean and was the data entered correctly

In light of this we will *not* include these 2 parameters in further analysis:
* MonthlyRate
* DailyRate
* HourlyRate

Final analysis will rely ONLY on MonthlyIncome


*PerformanceReview*

This parameter seems to be either incomplete or potentially have erroneous data. Again we have no context for the field, and the only values are 3 or 4



```r
# Treating values as factors not numerics
pie(
  count(employee.df, as.factor(PerformanceRating) )$n,
  labels = count(employee.df, as.factor(PerformanceRating))$"as.factor(PerformanceRating)",
  main = "Performance Rating: All Values"
  )
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-6-1.png)<!-- -->

Futhermore there doesn't appear to be compelling visual evidence of a difference in MonthlyIncome distribution for "4" vs "3" ratings

Given this we will also not be including PerformanceRating in the final analysis.


```r
# treating value as factors not numerics
boxplot(employee.df$MonthlyIncome ~ as.factor( employee.df$PerformanceRating))
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-7-1.png)<!-- -->


### Cleaned Data Set

Lets create a clean data set in light of our current data exploration. We have reduced the original data frame from 36 parameters down to 27.



```r
# Placing remaining variables into data frame
emp_clean.df <- employee.df %>% dplyr::select(
  MonthlyIncome,
  Attrition,
  Age,
  BusinessTravel,
  Department,
  DistanceFromHome,
  Education,
  EducationField,
  EnvironmentSatisfaction,
  Gender,
  JobInvolvement,
  JobLevel,
  JobRole,
  JobSatisfaction,
  MaritalStatus,
  NumCompaniesWorked,
  OverTime,
  PercentSalaryHike,
  RelationshipSatisfaction,
  StockOptionLevel,
  TotalWorkingYears,
  TrainingTimesLastYear,
  WorkLifeBalance,
  YearsAtCompany,
  YearsInCurrentRole,
  YearsSinceLastPromotion,
  YearsWithCurrManager
)

# Checking for missing or "na" values
# none found
table(emp_clean.df %>% is.na())
```

```
## 
## FALSE 
## 23490
```


### Data Prep for Income Response Variable

In order to allow us to do categorical analysis for the salary response variable we created a new response variable AnnualIncome based on these different categories:

* $0 - $30,000 annual
* $30,000 - $60,000 annual
* $60,000 - $90,000 annual
* $Over 90,000 annual

We will print out the 10 entries to validate the numerical values for MonthlyIncome are being correctly classified into the correct category for *AnnualIncome* which will be added for final analysis.


```r
# making categories for MonthlyIncome
# $30,000 annual
# $60,000 annual
# $90,000 annual
# $Over 90k annual

cut(emp_clean.df$MonthlyIncome, 
    breaks = c(0,2500,5000,7500,999999), 
    labels = c("Under_30k","30k_to_60k","60k_to_90k","Over_90k") 
    ) -> emp_clean.df$AnnualIncome

# validate accuracy new Annual Income categorical parameter
emp_clean.df[c(1,5,10,15,20,25,30,35,40,45),c("MonthlyIncome","AnnualIncome")]
```

```
##    MonthlyIncome AnnualIncome
## 1           4403   30k_to_60k
## 5           3760   30k_to_60k
## 10          5063   60k_to_90k
## 15          3423   30k_to_60k
## 20          5033   60k_to_90k
## 25          6949   60k_to_90k
## 30          4258   30k_to_60k
## 35          9679     Over_90k
## 40          2791   30k_to_60k
## 45          1223    Under_30k
```


### Data Prep: Categorical & Numeric 

To simplify some analytical and graphical exercises, we will create separate data frames for numeric and categoric data


```r
# categoric parameters only
emp_clean.df %>% keep(is.factor) -> emp_clean_factor.df
# put parameter names into vector to verify
emp_clean.df %>% keep(is.factor) %>% names -> catNames
catNames
```

```
## [1] "Attrition"      "BusinessTravel" "Department"     "EducationField"
## [5] "Gender"         "JobRole"        "MaritalStatus"  "OverTime"      
## [9] "AnnualIncome"
```

```r
# numeric parameters only
emp_clean.df %>% keep(is.numeric) -> emp_clean_numeric.df
# put parameter names into vector to verify
emp_clean.df %>% keep(is.numeric) %>% names -> numNames
numNames
```

```
##  [1] "MonthlyIncome"            "Age"                     
##  [3] "DistanceFromHome"         "Education"               
##  [5] "EnvironmentSatisfaction"  "JobInvolvement"          
##  [7] "JobLevel"                 "JobSatisfaction"         
##  [9] "NumCompaniesWorked"       "PercentSalaryHike"       
## [11] "RelationshipSatisfaction" "StockOptionLevel"        
## [13] "TotalWorkingYears"        "TrainingTimesLastYear"   
## [15] "WorkLifeBalance"          "YearsAtCompany"          
## [17] "YearsInCurrentRole"       "YearsSinceLastPromotion" 
## [19] "YearsWithCurrManager"
```

## Initial Exploratory Analysis

Now that our data is properly cleaned and setup we will go forward with some exploratory and visual analysis.

### Numeric Variables

We can quickly create some graphs to show us the range & spread for all numeric values


```r
# histogram of all numeric values
emp_clean_numeric.df %>% keep(is.numeric) %>% gather %>% ggplot(aes(x = value)) + facet_wrap(~key, scales = "free")+ geom_histogram()
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-11-1.png)<!-- -->

When we plot all the numeric variables against MonthlyIncome, we observe the following:

* Evidence of a strong relationship with JobLevel
* Evidence of a moderate relationship with TotalWorkingYears
* Evidence of a moderate relationship with YearsAtCompany
* Evidence of a moderate relationship with YearsInCurrentRole



```r
plot_vs_response <- function(x){
  plot(emp_clean_numeric.df$MonthlyIncome ~ emp_clean_numeric.df[[x]], xlab = x)
  lw1 <- loess(emp_clean_numeric.df$MonthlyIncome ~ emp_clean_numeric.df[[x]])
  j <- order(emp_clean_numeric.df[[x]])
  lines(emp_clean_numeric.df[[x]][j],lw1$fitted[j],col="red",lwd=3)
}

# Lapply on all xnames values and assing to list object
# plot all numeric variables as x vs response with lapply
# use numNames vector previously defined 

lapply(numNames, plot_vs_response) 
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-12-1.png)<!-- -->![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-12-2.png)<!-- -->![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-12-3.png)<!-- -->

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : pseudoinverse used at 3
```

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : neighborhood radius 1
```

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : reciprocal condition number 0
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-12-4.png)<!-- -->

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : pseudoinverse used at 4.015
```

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : neighborhood radius 2.015
```

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : reciprocal condition number 2.2838e-017
```

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : There are other near singularities as well. 1
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-12-5.png)<!-- -->

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : pseudoinverse used at 0.985
```

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : neighborhood radius 2.015
```

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : reciprocal condition number 1.0873e-016
```

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : There are other near singularities as well. 4.0602
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-12-6.png)<!-- -->

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : pseudoinverse used at 0.98
```

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : neighborhood radius 2.02
```

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : reciprocal condition number 1.2664e-016
```

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : There are other near singularities as well. 4
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-12-7.png)<!-- -->

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : pseudoinverse used at 4.015
```

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : neighborhood radius 2.015
```

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : reciprocal condition number 2.017e-016
```

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : There are other near singularities as well. 1
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-12-8.png)<!-- -->![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-12-9.png)<!-- -->![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-12-10.png)<!-- -->

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : pseudoinverse used at 4.015
```

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : neighborhood radius 2.015
```

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : reciprocal condition number 1.3981e-016
```

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : There are other near singularities as well. 1
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-12-11.png)<!-- -->

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : pseudoinverse used at -0.015
```

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : neighborhood radius 1.015
```

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : reciprocal condition number 0
```

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : There are other near singularities as well. 1
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-12-12.png)<!-- -->![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-12-13.png)<!-- -->

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : pseudoinverse used at 2
```

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : neighborhood radius 1
```

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : reciprocal condition number 0
```

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : There are other near singularities as well. 1
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-12-14.png)<!-- -->

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : pseudoinverse used at 0.985
```

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : neighborhood radius 2.015
```

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : reciprocal condition number 1.0045e-016
```

```
## Warning in simpleLoess(y, x, w, span, degree = degree, parametric =
## parametric, : There are other near singularities as well. 4.0602
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-12-15.png)<!-- -->![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-12-16.png)<!-- -->![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-12-17.png)<!-- -->![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-12-18.png)<!-- -->![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-12-19.png)<!-- -->

```
## [[1]]
## NULL
## 
## [[2]]
## NULL
## 
## [[3]]
## NULL
## 
## [[4]]
## NULL
## 
## [[5]]
## NULL
## 
## [[6]]
## NULL
## 
## [[7]]
## NULL
## 
## [[8]]
## NULL
## 
## [[9]]
## NULL
## 
## [[10]]
## NULL
## 
## [[11]]
## NULL
## 
## [[12]]
## NULL
## 
## [[13]]
## NULL
## 
## [[14]]
## NULL
## 
## [[15]]
## NULL
## 
## [[16]]
## NULL
## 
## [[17]]
## NULL
## 
## [[18]]
## NULL
## 
## [[19]]
## NULL
```



### Categorical Variables


```r
# Defining Plot Function
catplot <- function(df, x,y){
  ggplot(data = df, aes_string(x = x, fill = y)) + 
    geom_bar(position = "fill", alpha = 0.9) + 
    coord_flip()
}
```

*Annual Income*

The breakdown of annual income shows a reasonably even distribution for the different income categories. The largest delta that exist is the 145 employees that make under 30k per year and the 298 that make between 30k to 60k. While it is about twice as much that is still within acceptable parameters for most models



```r
# AnnualIncome: Total Breakdown
count(emp_clean_factor.df, AnnualIncome)
```

```
## # A tibble: 4 x 2
##   AnnualIncome     n
##   <fctr>       <int>
## 1 Under_30k      145
## 2 30k_to_60k     298
## 3 60k_to_90k     191
## 4 Over_90k       236
```

```r
# Pie chart
pie(
  count(emp_clean_factor.df, AnnualIncome)$n,
  labels = count(emp_clean_factor.df, AnnualIncome)$AnnualIncome,
  main = "Annual Income"
)
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-14-1.png)<!-- -->

Looking at a comparision of each the categorical variables for the different levels of AnnualIncome, we can see the following:

* JobRoles for "Research Director" and "Manager" have almost exclusively higher annual salaries of over $90,000 
* JobRoles for "Healthcare Representative," "Manufacturing Director," and "Sales Executive" have no employees making under $30,00 per year
* JobRoles for "Lab Technician," "Sales Rep," "Research Scientist" have almost no employees in the over $90,000 category

Most of the other categories were fairly evenly distributed. Thus for predicting salary the JobRole will likely be a critical factor.




```r
# Excluding Attrition and AnnualIncome
xnames <- catNames[-c(1,9)]

# Lapply on all xnames values and assing to list object
catplot.list <-lapply(xnames, function(x) catplot(emp_clean_factor.df,x, "AnnualIncome"))
# output plot grid
cowplot::plot_grid(plotlist = catplot.list)
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-15-1.png)<!-- -->


*Attrition*

There are significantly more employees that do not leave their companies vs the ones that do: 730 "No" vs only 140 "Yes." Thus we just need to keep this in mind as we look for changes in the ratios or percentages rather than the raw numbers themselves.



```r
# Attrition: Total Breakdown
count(emp_clean_factor.df, Attrition)
```

```
## # A tibble: 2 x 2
##   Attrition     n
##   <fctr>    <int>
## 1 No          730
## 2 Yes         140
```

```r
# Pie chart
pie(
  count(emp_clean_factor.df, Attrition)$n,
  labels = count(emp_clean_factor.df, Attrition)$Attrition,
  main = "Attrition"
)
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-16-1.png)<!-- -->

Looking at a comparision of each the categorical variables for "Yes" vs "No" Attrition, we can see significant differences for the following:

* OverTime category for "Yes"
* JobRole category for "Sales Representative"
* MaritalStatus category for "Single"
* AnnualIncome category for "Under_30k"



```r
# Excluding Attrition
xnames <- catNames[-1]

# Lapply on all xnames values and assing to list object
catplot.list <-lapply(xnames, function(x) catplot(emp_clean_factor.df,x, "Attrition"))
# output plot grid
cowplot::plot_grid(plotlist = catplot.list)
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-17-1.png)<!-- -->


## Feature Modeling

Now we will try to determine which variables are the most influential for employee attrition and salary.

### Relationship Between Response Variables

We have 2 response variables to examine:
* Attrition: binomial / categorical [i.e. "yes" or "no"]
* MonthlyIncome: numerical salary by month

First we use a boxplot to compare the MonthlyIncome distribution for "Yes" vs "No" Attrition groups. We an certainly see that visually the distributions appear to be different from each other.


```r
# Comparing MonthlyIncome by different Attrition levels
boxplot(emp_clean.df$MonthlyIncome ~ emp_clean.df$Attrition,
        main = "Monthly Income by Attrition",
        horizontal = T,
        xlab = "Dollars" )
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-18-1.png)<!-- -->

We split the income values into separate vectors and run some exploratory analysis on them. We see that there are about 5 times as many employees who stay vs those who leave. It also appears the mean MonthlyIncome for employees who leave is somewhat lower: 

* $4764.79 for empoyees who leave 
* $6702 for those that stay

There is also greater variance in between the two groups as well:
* The "Yes" group having a standard deviation of $3786.389
* The "No" group having a standard deviation of $4675.472 



```r
# make vectors for monthly income by attrition
emp_clean.df$MonthlyIncome[emp_clean.df$Attrition == "Yes"] -> mo_income_yes
emp_clean.df$MonthlyIncome[emp_clean.df$Attrition == "No"] -> mo_income_no

# number of yes vs no attrition
length(mo_income_yes)
```

```
## [1] 140
```

```r
length(mo_income_no)
```

```
## [1] 730
```

```r
summary(mo_income_yes) 
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##    1081    2342    3171    4765    5839   19859
```

```r
summary(mo_income_no) 
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##    1129    3162    5208    6702    8736   19999
```

```r
sd(mo_income_yes) 
```

```
## [1] 3786.389
```

```r
sd(mo_income_no) 
```

```
## [1] 4675.472
```

```r
# histogram of each
hist(mo_income_yes, xlab = "Dollars", main = "Salary for Attrition: Yes")
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-19-1.png)<!-- -->

```r
hist(mo_income_no, xlab = "Dollars", main = "Salary for Attrition: No")
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-19-2.png)<!-- -->

Because both distrubitions differ significantly from normality we log tranform them in order to do a t-test and see if there is evidence that the distributions differ significantly from each other. 

The QQ Plot of the log transformed MonthlyIncome for each group shows much better adherence to normality, and brings the variance of each group much closer together. 

The T-test itself shows there is a significant difference between the Monthly Incomes of "Yes" vs "No" attrition groups, p-value < 0.0001. This is strong evidence that MonthlyIncome has an effect on Attrition



```r
### T-test to compare each group

# taking log of each
qqnorm(log(mo_income_yes))
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-20-1.png)<!-- -->

```r
qqnorm(log(mo_income_no))
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-20-2.png)<!-- -->

```r
# even using the log of both still get 
# to be safe we account for unequal variance
t.test(log(mo_income_no), log(mo_income_yes), var.equal = F) 
```

```
## 
## 	Welch Two Sample t-test
## 
## data:  log(mo_income_no) and log(mo_income_yes)
## t = 5.9593, df = 195.72, p-value = 1.16e-08
## alternative hypothesis: true difference in means is not equal to 0
## 95 percent confidence interval:
##  0.2391013 0.4756331
## sample estimates:
## mean of x mean of y 
##  8.595187  8.237820
```


## Random Forest for Influential Factors

We still have the challenge of 25 parameters to determine MonthlyIncome, and 26 parameters [including MonthlyIncome] to predict Attrition.

Here we are going to use Random Forest to help see which of these are the most influential and cross-reference against common industry expertise. It is essentially a collection or "ensemble"" of individual decision trees, by first generating a random sample of the original data with replacement, aka "bootstrapping." Multiple subsets of trees are built using a randomly select set of our data set's variables.

The variable importance plot tells us how important each variable is in classifying the data. The plot on the left "Mean Decrease in Accuracy" is the one we will be focusing on. Essentially translates that the more important the parameter, the less accurate the random forest model becomes when it is omitted.


*Attrition*

Top 5 most influential were: 

* OverTime
* MonthlyIncome
* JobRole
* StockOptionLevel
* Age



```r
# excluding redundant AnnualIncome
emp_clean.df[,-c(28)] -> emp_attrition_forest.df

# Random forest for Attrition
emp_attrition_forest.rf <- randomForest(Attrition ~., 
                                     data = emp_attrition_forest.df, 
                                     importance = TRUE)
varImpPlot(emp_attrition_forest.rf)
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-21-1.png)<!-- -->


### Deeper look at OverTime

The ratio or percentage of employees who leave the company is significantly higher for employees that do not get overtime vs those that do:

* OverTime "Yes": 172 stay vs 80 leave
* OverTime "No": 60 stay vs 558 leave



```r
barplot(
  emp_clean_factor.df %>% group_by(Attrition) %>% count(OverTime) %>% .[[3]],
  col = emp_clean_factor.df %>% group_by(Attrition) %>% count(OverTime) %>% .[[2]],
  names.arg = emp_clean_factor.df %>% group_by(Attrition) %>% count(OverTime) %>% .[[1]],
  legend.text = emp_clean_factor.df %>% group_by(Attrition) %>% count(OverTime) %>% .[[2]],
  horiz = T,
  xlab = "Number of Employees",
  main = "Attrition: OverTime"
)
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-22-1.png)<!-- -->


### Deeper look at AnnualIncome

Again the ratio of employees that leave is higher depending on what level they are in AnnualIncome category:

* Under_30k: 96 stay vs 49 leave
* 30k_to_60k: 248 stay vs 50 leave
* 60k_to_90k: 175 stay vs 16 leave
* Over_90k: 211 stay vs 25 leave



```r
barplot(
  emp_clean_factor.df %>% group_by(Attrition) %>% count(AnnualIncome) %>% .[[3]],
  col = emp_clean_factor.df %>% group_by(Attrition) %>% count(AnnualIncome) %>% .[[2]],
  names.arg = emp_clean_factor.df %>% group_by(Attrition) %>% count(AnnualIncome) %>% .[[1]],
  legend.text = emp_clean_factor.df %>% group_by(Attrition) %>% count(AnnualIncome) %>% .[[2]],
  horiz = T,
  xlab = "Number of Employees",
  main = "Attrition: Annual Income"
)
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-23-1.png)<!-- -->


### Deeper look at JobRole

Certain job roles have a different attrition ratio. Sales Reps is almost a 1 for 1 with 29 stay vs 24 leave. On the other end Research Directors and Manufacturing Directors have the lowest attrition [50 vs 1] and [85 vs 2] respectively.


```r
barplot(
  emp_clean_factor.df %>% group_by(Attrition) %>% count(JobRole) %>% .[[3]],
  col = rainbow(9),
  names.arg = emp_clean_factor.df %>% group_by(Attrition) %>% count(JobRole) %>% .[[1]],
  legend.text = emp_clean_factor.df %>% group_by(Attrition) %>% count(JobRole) %>% .[[2]],
  horiz = T,
  xlab = "Number of Employees",
  main = "Attrition: JobRole"
)
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-24-1.png)<!-- -->



*MonthlyIncome*
Top 5 most influential were: 

* JobLevel
* JobRole
* TotalWorkingYears
* YearsAtCompany
* Age



```r
# excluding redundant AnnualIncome and Attrition
emp_clean.df[,-c(2,28)] -> emp_salary_forest.df

# Random forest for MonthlyIncome
emp_salary_forest.rf <- randomForest(MonthlyIncome ~., 
                                     data = emp_salary_forest.df, 
                                     importance = TRUE)
varImpPlot(emp_salary_forest.rf)
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-25-1.png)<!-- -->


## Monthly Income

### Correlation in Numeric Variables

We can see the highest correlation that exists for MonthlyIncome very much align to common sense:

* MonthlyIncome & JobLevel = 0.95
* MonthlyIncome & TotalWorkingYears = 0.78


```r
# creating function for heatmap using corrplot::corrplot()

correlator  <-  function(df){
  df %>%
    keep(is.numeric) %>%
    tidyr::drop_na() %>%
    cor %>%
    corrplot( addCoef.col = "white", number.digits = 2,
              number.cex = 0.5, method="square",
              order="hclust", title="Variable Corr Heatmap",
              tl.srt=45, tl.cex = 0.8)
}
```

### Closer Look At Job Level

A closer examination supports evidence of a relationship between MonthlyIncome and JobLevel


```r
# monthly income by job level
plot(emp_clean.df$JobLevel, emp_clean.df$MonthlyIncome, main = "Monthly Income vs Job Level",
     xlab = "Job Level",
     ylab = "Dollars")
lines(lowess(emp_clean.df$JobLevel, emp_clean.df$MonthlyIncome), col = "red")
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-27-1.png)<!-- -->

### Closer Look At Total Working Years

A closer examination supports evidence of a relationship between MonthlyIncome and TotalWorkingYears


```r
# monthly income total working years
plot(emp_clean.df$TotalWorkingYears, emp_clean.df$MonthlyIncome, main = "Monthly Income vs Total Working Years",
     xlab = "Years",
     ylab = "Dollars")
lines(lowess(emp_clean.df$TotalWorkingYears, emp_clean.df$MonthlyIncome), col = "red")
```

![](CaseStudy2_DDS-6306_JeremyOtsap_files/figure-html/unnamed-chunk-28-1.png)<!-- -->

### Linear Regression Model: MonthlyIncome

First we run a linear regression against MonthlyIncome. Note that R will automatically convert factors into dummy variables for us. Uur model shows the following as important parameters that all have p-value <= 0.05:

* BusinessTravel: Travel_Rarely
* JobLevel
* JobRole: Laboratory Technician
* JobRole: Manager
* JobRole: Research Director
* JobRole: Research Scientist
* TotalWorkingYears
* YearsSinceLastPromotion 

However we need to be congnizant of covariance. When we examine our model we notice that JobRole and Department have extremely high VIF. Typically a VIF value should never be larger than 10.

In light of this we will update our model



```r
### Linear Regression on MonthlyIncome

#NOTE: Re-using Random Forest DF that excluded Attrition and AnnualIncome 
emp_salary_forest.df -> emp_salary_reg.df
# First create model on all variables
lm(MonthlyIncome ~ ., data =  emp_salary_reg.df) -> emp_salary.lm
summary(emp_salary.lm)
```

```
## 
## Call:
## lm(formula = MonthlyIncome ~ ., data = emp_salary_reg.df)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -3737.2  -656.2   -33.6   589.1  4136.7 
## 
## Coefficients:
##                                  Estimate Std. Error t value Pr(>|t|)    
## (Intercept)                      -606.140    668.262  -0.907 0.364649    
## Age                                -1.639      5.654  -0.290 0.771955    
## BusinessTravelTravel_Frequently   182.855    141.863   1.289 0.197775    
## BusinessTravelTravel_Rarely       369.777    120.413   3.071 0.002204 ** 
## DepartmentResearch & Development  114.497    478.229   0.239 0.810839    
## DepartmentSales                  -430.542    489.251  -0.880 0.379113    
## DistanceFromHome                   -6.311      4.559  -1.384 0.166647    
## Education                         -30.058     37.243  -0.807 0.419856    
## EducationFieldLife Sciences       131.421    369.673   0.356 0.722301    
## EducationFieldMarketing           106.003    391.782   0.271 0.786792    
## EducationFieldMedical              26.071    370.560   0.070 0.943927    
## EducationFieldOther                84.344    395.567   0.213 0.831205    
## EducationFieldTechnical Degree     87.267    385.419   0.226 0.820930    
## EnvironmentSatisfaction           -10.055     33.561  -0.300 0.764551    
## GenderMale                        119.237     74.663   1.597 0.110648    
## JobInvolvement                     17.245     52.444   0.329 0.742365    
## JobLevel                         2798.801     83.435  33.544  < 2e-16 ***
## JobRoleHuman Resources           -191.727    516.673  -0.371 0.710673    
## JobRoleLaboratory Technician     -603.937    171.759  -3.516 0.000462 ***
## JobRoleManager                   4199.953    282.586  14.863  < 2e-16 ***
## JobRoleManufacturing Director     153.533    170.023   0.903 0.366782    
## JobRoleResearch Director         4012.539    219.451  18.284  < 2e-16 ***
## JobRoleResearch Scientist        -331.361    170.699  -1.941 0.052572 .  
## JobRoleSales Executive            487.672    358.943   1.359 0.174632    
## JobRoleSales Representative       109.404    392.699   0.279 0.780625    
## JobSatisfaction                    23.515     32.974   0.713 0.475958    
## MaritalStatusMarried               70.239    100.024   0.702 0.482740    
## MaritalStatusSingle                16.787    134.568   0.125 0.900753    
## NumCompaniesWorked                  8.757     16.798   0.521 0.602279    
## OverTimeYes                         7.519     81.140   0.093 0.926191    
## PercentSalaryHike                   1.193      9.961   0.120 0.904732    
## RelationshipSatisfaction           16.304     33.297   0.490 0.624510    
## StockOptionLevel                    6.157     56.901   0.108 0.913859    
## TotalWorkingYears                  49.926     10.999   4.539 6.48e-06 ***
## TrainingTimesLastYear              23.730     29.182   0.813 0.416350    
## WorkLifeBalance                   -43.459     51.593  -0.842 0.399844    
## YearsAtCompany                     -2.131     13.620  -0.156 0.875706    
## YearsInCurrentRole                  3.479     17.006   0.205 0.837942    
## YearsSinceLastPromotion            29.344     15.258   1.923 0.054804 .  
## YearsWithCurrManager              -26.775     16.731  -1.600 0.109901    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 1061 on 830 degrees of freedom
## Multiple R-squared:  0.9491,	Adjusted R-squared:  0.9468 
## F-statistic: 397.2 on 39 and 830 DF,  p-value: < 2.2e-16
```

```r
# Then test for VIF
vif(emp_salary.lm)
```

```
##                                GVIF Df GVIF^(1/(2*Df))
## Age                        1.966832  1        1.402438
## BusinessTravel             1.110032  2        1.026441
## Department               110.874985  2        3.244953
## DistanceFromHome           1.062561  1        1.030806
## Education                  1.120870  1        1.058711
## EducationField             2.898610  5        1.112292
## EnvironmentSatisfaction    1.051126  1        1.025244
## Gender                     1.039965  1        1.019787
## JobInvolvement             1.051408  1        1.025382
## JobLevel                   6.387536  1        2.527358
## JobRole                  494.745698  8        1.473665
## JobSatisfaction            1.042825  1        1.021188
## MaritalStatus              1.984091  2        1.186835
## NumCompaniesWorked         1.384089  1        1.176473
## OverTime                   1.047147  1        1.023302
## PercentSalaryHike          1.034879  1        1.017290
## RelationshipSatisfaction   1.040543  1        1.020070
## StockOptionLevel           1.839351  1        1.356227
## TotalWorkingYears          5.273175  1        2.296339
## TrainingTimesLastYear      1.064991  1        1.031984
## WorkLifeBalance            1.041859  1        1.020715
## YearsAtCompany             5.192457  1        2.278696
## YearsInCurrentRole         2.957481  1        1.719733
## YearsSinceLastPromotion    1.824576  1        1.350768
## YearsWithCurrManager       2.761474  1        1.661768
```

Our model is looking better but we still have two variables with a VIF higher than 5:
* TotalWorkingYears
* YearsAtCompany 

Given that TotalWorkingYears is one of the critical parameters identified by the model, we will leave it in for now and remove only YearsAtCompany

We update our data set to remove Department, JobRole, and YearsAtCompany and rerun the regression model. Important Factors that all have p-value <= 0.05:

* BusinessTravel: Travel_Rarely
* JobLevel
* JobRole: Laboratory Technician
* JobRole: Manager
* JobRole: Research Director
* JobRole: Research Scientist
* TotalWorkingYears
* YearsSinceLastPromotion 

We can see now that all parameters have a VIF lower than 5


```r
# Rerun with JobRole and Department removed
emp_salary_reg.df[,-c(4,12,23)] -> emp_salary_reg.df
# create model on remaining variables
lm(MonthlyIncome ~ ., data =  emp_salary_reg.df) -> emp_salary.lm
summary(emp_salary.lm)
```

```
## 
## Call:
## lm(formula = MonthlyIncome ~ ., data = emp_salary_reg.df)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -5861.0  -769.5    -6.5   742.5  4301.9 
## 
## Coefficients:
##                                  Estimate Std. Error t value Pr(>|t|)    
## (Intercept)                     -1806.637    619.214  -2.918  0.00362 ** 
## Age                                -2.169      7.224  -0.300  0.76405    
## BusinessTravelTravel_Frequently    11.756    181.331   0.065  0.94833    
## BusinessTravelTravel_Rarely       320.527    154.247   2.078  0.03801 *  
## DistanceFromHome                  -15.666      5.845  -2.680  0.00750 ** 
## Education                          16.011     47.516   0.337  0.73624    
## EducationFieldLife Sciences      -300.034    364.462  -0.823  0.41061    
## EducationFieldMarketing          -662.914    383.450  -1.729  0.08421 .  
## EducationFieldMedical            -263.247    367.030  -0.717  0.47343    
## EducationFieldOther              -373.388    404.501  -0.923  0.35623    
## EducationFieldTechnical Degree   -299.439    390.652  -0.767  0.44359    
## EnvironmentSatisfaction           -64.413     42.860  -1.503  0.13325    
## GenderMale                         75.961     95.906   0.792  0.42856    
## JobInvolvement                     95.049     66.825   1.422  0.15529    
## JobLevel                         3745.200     69.721  53.717  < 2e-16 ***
## JobSatisfaction                    -4.059     42.325  -0.096  0.92362    
## MaritalStatusMarried              150.965    128.169   1.178  0.23919    
## MaritalStatusSingle                -6.329    171.898  -0.037  0.97064    
## NumCompaniesWorked                 -8.432     20.860  -0.404  0.68615    
## OverTimeYes                        25.636    103.697   0.247  0.80480    
## PercentSalaryHike                   9.370     12.802   0.732  0.46445    
## RelationshipSatisfaction           23.958     42.861   0.559  0.57633    
## StockOptionLevel                   -9.418     72.987  -0.129  0.89736    
## TotalWorkingYears                  68.280     13.201   5.172 2.89e-07 ***
## TrainingTimesLastYear              20.487     37.236   0.550  0.58232    
## WorkLifeBalance                   -27.215     66.220  -0.411  0.68119    
## YearsInCurrentRole                -16.614     19.871  -0.836  0.40335    
## YearsSinceLastPromotion             3.586     18.654   0.192  0.84759    
## YearsWithCurrManager              -50.185     19.476  -2.577  0.01014 *  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 1369 on 841 degrees of freedom
## Multiple R-squared:  0.9141,	Adjusted R-squared:  0.9113 
## F-statistic: 319.8 on 28 and 841 DF,  p-value: < 2.2e-16
```

```r
# Then test for VIF
vif(emp_salary.lm)
```

```
##                              GVIF Df GVIF^(1/(2*Df))
## Age                      1.926750  1        1.388074
## BusinessTravel           1.071485  2        1.017411
## DistanceFromHome         1.048116  1        1.023775
## Education                1.094953  1        1.046400
## EducationField           1.149730  5        1.014051
## EnvironmentSatisfaction  1.028756  1        1.014276
## Gender                   1.029742  1        1.014762
## JobInvolvement           1.024454  1        1.012153
## JobLevel                 2.676686  1        1.636058
## JobSatisfaction          1.031099  1        1.015431
## MaritalStatus            1.930257  2        1.178701
## NumCompaniesWorked       1.280881  1        1.131760
## OverTime                 1.026384  1        1.013106
## PercentSalaryHike        1.025949  1        1.012891
## RelationshipSatisfaction 1.034677  1        1.017191
## StockOptionLevel         1.816129  1        1.347638
## TotalWorkingYears        4.558435  1        2.135049
## TrainingTimesLastYear    1.040561  1        1.020079
## WorkLifeBalance          1.029981  1        1.014880
## YearsInCurrentRole       2.423289  1        1.556692
## YearsSinceLastPromotion  1.636534  1        1.279271
## YearsWithCurrManager     2.245623  1        1.498540
```


### Stepwise Selection

Now that we have used multicollinearity to reduce to 23 parameters, we now will run Stepwise Feature Selection to find the 6 most influential variables and compare them to both what the Random Forest found as well as the prior Linear Regression.

Important Factors that all have p-value <= 0.05:

* BusinessTravel: Travel_Rarely
* DistanceFromHome 
* EducationField: Marketing
* JobLevel
* TotalWorkingYears
* YearsWithCurrManager

We can see the RMSE of this model is $1377.65

And the Adjusted R-squared is 0.9128, which means an estimated 91.28% of the MonthlyIncome variable can be accounted for by this model.



```r
# set method to use 5-fold cross-validation
trainControl(method = "cv", number = 5) -> train.cv

train(MonthlyIncome ~ .,
  data = emp_salary_reg.df,
  method = "lmStepAIC",
  trControl = train.cv
) -> emp_salary.step
```

```
## Start:  AIC=10092.81
## .outcome ~ Age + BusinessTravelTravel_Frequently + BusinessTravelTravel_Rarely + 
##     DistanceFromHome + Education + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     GenderMale + JobInvolvement + JobLevel + JobSatisfaction + 
##     MaritalStatusMarried + MaritalStatusSingle + NumCompaniesWorked + 
##     OverTimeYes + PercentSalaryHike + RelationshipSatisfaction + 
##     StockOptionLevel + TotalWorkingYears + TrainingTimesLastYear + 
##     WorkLifeBalance + YearsInCurrentRole + YearsSinceLastPromotion + 
##     YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - BusinessTravelTravel_Frequently   1         48 1271140833 10091
## - OverTimeYes                       1       3408 1271144192 10091
## - Age                               1       7615 1271148400 10091
## - StockOptionLevel                  1      24152 1271164937 10091
## - TrainingTimesLastYear             1      24318 1271165102 10091
## - MaritalStatusSingle               1      79255 1271220040 10091
## - NumCompaniesWorked                1     169386 1271310171 10091
## - GenderMale                        1     194605 1271335390 10091
## - EducationFieldMedical             1     228785 1271369570 10091
## - EducationFieldOther               1     241313 1271382098 10091
## - `EducationFieldTechnical Degree`  1     274456 1271415241 10091
## - RelationshipSatisfaction          1     289911 1271430696 10091
## - `EducationFieldLife Sciences`     1     292838 1271433623 10091
## - WorkLifeBalance                   1     293143 1271433928 10091
## - Education                         1     300129 1271440914 10091
## - JobSatisfaction                   1     673996 1271814781 10091
## - YearsInCurrentRole                1     892520 1272033304 10091
## - YearsSinceLastPromotion           1    1043395 1272184180 10091
## - JobInvolvement                    1    1045281 1272186066 10091
## - MaritalStatusMarried              1    1767419 1272908204 10092
## - EducationFieldMarketing           1    2108546 1273249331 10092
## - PercentSalaryHike                 1    2151669 1273292454 10092
## - EnvironmentSatisfaction           1    3205567 1274346352 10093
## <none>                                           1271140785 10093
## - BusinessTravelTravel_Rarely       1    7818619 1278959403 10095
## - YearsWithCurrManager              1    9503910 1280644695 10096
## - DistanceFromHome                  1   17780910 1288921694 10100
## - TotalWorkingYears                 1   39414811 1310555596 10112
## - JobLevel                          1 4341332780 5612473565 11124
## 
## Step:  AIC=10090.81
## .outcome ~ Age + BusinessTravelTravel_Rarely + DistanceFromHome + 
##     Education + `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     JobSatisfaction + MaritalStatusMarried + MaritalStatusSingle + 
##     NumCompaniesWorked + OverTimeYes + PercentSalaryHike + RelationshipSatisfaction + 
##     StockOptionLevel + TotalWorkingYears + TrainingTimesLastYear + 
##     WorkLifeBalance + YearsInCurrentRole + YearsSinceLastPromotion + 
##     YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - OverTimeYes                       1       3365 1271144198 10089
## - Age                               1       7580 1271148414 10089
## - StockOptionLevel                  1      24252 1271165085 10089
## - TrainingTimesLastYear             1      24601 1271165435 10089
## - MaritalStatusSingle               1      79410 1271220243 10089
## - NumCompaniesWorked                1     169910 1271310743 10089
## - GenderMale                        1     194828 1271335662 10089
## - EducationFieldMedical             1     228904 1271369738 10089
## - EducationFieldOther               1     241314 1271382147 10089
## - `EducationFieldTechnical Degree`  1     274575 1271415409 10089
## - RelationshipSatisfaction          1     290268 1271431101 10089
## - `EducationFieldLife Sciences`     1     292876 1271433709 10089
## - WorkLifeBalance                   1     293291 1271434124 10089
## - Education                         1     300445 1271441278 10089
## - JobSatisfaction                   1     675728 1271816561 10089
## - YearsInCurrentRole                1     892803 1272033636 10089
## - YearsSinceLastPromotion           1    1043790 1272184623 10089
## - JobInvolvement                    1    1047181 1272188014 10089
## - MaritalStatusMarried              1    1770456 1272911289 10090
## - EducationFieldMarketing           1    2108504 1273249337 10090
## - PercentSalaryHike                 1    2151736 1273292569 10090
## - EnvironmentSatisfaction           1    3205570 1274346403 10091
## <none>                                           1271140833 10091
## - YearsWithCurrManager              1    9521403 1280662236 10094
## - BusinessTravelTravel_Rarely       1   16803394 1287944227 10098
## - DistanceFromHome                  1   17808418 1288949252 10098
## - TotalWorkingYears                 1   39561299 1310702132 10110
## - JobLevel                          1 4345758787 5616899620 11123
## 
## Step:  AIC=10088.81
## .outcome ~ Age + BusinessTravelTravel_Rarely + DistanceFromHome + 
##     Education + `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     JobSatisfaction + MaritalStatusMarried + MaritalStatusSingle + 
##     NumCompaniesWorked + PercentSalaryHike + RelationshipSatisfaction + 
##     StockOptionLevel + TotalWorkingYears + TrainingTimesLastYear + 
##     WorkLifeBalance + YearsInCurrentRole + YearsSinceLastPromotion + 
##     YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - Age                               1       7317 1271151515 10087
## - StockOptionLevel                  1      24677 1271168875 10087
## - TrainingTimesLastYear             1      26405 1271170603 10087
## - MaritalStatusSingle               1      79062 1271223259 10087
## - NumCompaniesWorked                1     169662 1271313860 10087
## - GenderMale                        1     194289 1271338487 10087
## - EducationFieldMedical             1     230508 1271374706 10087
## - EducationFieldOther               1     243368 1271387566 10087
## - `EducationFieldTechnical Degree`  1     274350 1271418548 10087
## - RelationshipSatisfaction          1     289766 1271433964 10087
## - WorkLifeBalance                   1     292737 1271436935 10087
## - `EducationFieldLife Sciences`     1     294653 1271438851 10087
## - Education                         1     304294 1271448492 10087
## - JobSatisfaction                   1     678873 1271823071 10087
## - YearsInCurrentRole                1     892501 1272036698 10087
## - YearsSinceLastPromotion           1    1040981 1272185179 10087
## - JobInvolvement                    1    1047286 1272191483 10087
## - MaritalStatusMarried              1    1782677 1272926875 10088
## - EducationFieldMarketing           1    2113454 1273257652 10088
## - PercentSalaryHike                 1    2156175 1273300373 10088
## - EnvironmentSatisfaction           1    3240427 1274384625 10089
## <none>                                           1271144198 10089
## - YearsWithCurrManager              1    9522262 1280666460 10092
## - BusinessTravelTravel_Rarely       1   16812588 1287956786 10096
## - DistanceFromHome                  1   17891638 1289035836 10096
## - TotalWorkingYears                 1   39642235 1310786433 10108
## - JobLevel                          1 4345795052 5616939249 11121
## 
## Step:  AIC=10086.82
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + Education + 
##     `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     JobSatisfaction + MaritalStatusMarried + MaritalStatusSingle + 
##     NumCompaniesWorked + PercentSalaryHike + RelationshipSatisfaction + 
##     StockOptionLevel + TotalWorkingYears + TrainingTimesLastYear + 
##     WorkLifeBalance + YearsInCurrentRole + YearsSinceLastPromotion + 
##     YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - StockOptionLevel                  1      23955 1271175471 10085
## - TrainingTimesLastYear             1      26916 1271178431 10085
## - MaritalStatusSingle               1      82078 1271233593 10085
## - NumCompaniesWorked                1     164597 1271316113 10085
## - GenderMale                        1     192607 1271344122 10085
## - EducationFieldMedical             1     230195 1271381710 10085
## - EducationFieldOther               1     243539 1271395055 10085
## - `EducationFieldTechnical Degree`  1     273139 1271424655 10085
## - RelationshipSatisfaction          1     291002 1271442517 10085
## - `EducationFieldLife Sciences`     1     291996 1271443511 10085
## - WorkLifeBalance                   1     293873 1271445388 10085
## - Education                         1     326895 1271478411 10085
## - JobSatisfaction                   1     679852 1271831367 10085
## - YearsInCurrentRole                1     905897 1272057412 10085
## - YearsSinceLastPromotion           1    1037839 1272189355 10085
## - JobInvolvement                    1    1052439 1272203954 10085
## - MaritalStatusMarried              1    1781747 1272933263 10086
## - EducationFieldMarketing           1    2110255 1273261770 10086
## - PercentSalaryHike                 1    2170787 1273322302 10086
## - EnvironmentSatisfaction           1    3240154 1274391669 10087
## <none>                                           1271151515 10087
## - YearsWithCurrManager              1    9573820 1280725335 10090
## - BusinessTravelTravel_Rarely       1   16805278 1287956793 10094
## - DistanceFromHome                  1   17891381 1289042896 10094
## - TotalWorkingYears                 1   51539232 1322690748 10112
## - JobLevel                          1 4359999307 5631150822 11121
## 
## Step:  AIC=10084.83
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + Education + 
##     `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     JobSatisfaction + MaritalStatusMarried + MaritalStatusSingle + 
##     NumCompaniesWorked + PercentSalaryHike + RelationshipSatisfaction + 
##     TotalWorkingYears + TrainingTimesLastYear + WorkLifeBalance + 
##     YearsInCurrentRole + YearsSinceLastPromotion + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - TrainingTimesLastYear             1      28738 1271204209 10083
## - NumCompaniesWorked                1     159460 1271334931 10083
## - GenderMale                        1     192249 1271367719 10083
## - EducationFieldMedical             1     228392 1271403863 10083
## - EducationFieldOther               1     251079 1271426549 10083
## - MaritalStatusSingle               1     259728 1271435199 10083
## - `EducationFieldTechnical Degree`  1     271724 1271447195 10083
## - RelationshipSatisfaction          1     286303 1271461774 10083
## - WorkLifeBalance                   1     287673 1271463143 10083
## - `EducationFieldLife Sciences`     1     290328 1271465799 10083
## - Education                         1     329013 1271504484 10083
## - JobSatisfaction                   1     670778 1271846248 10083
## - YearsInCurrentRole                1     893070 1272068541 10083
## - YearsSinceLastPromotion           1    1028290 1272203760 10083
## - JobInvolvement                    1    1052919 1272228390 10083
## - MaritalStatusMarried              1    1850978 1273026449 10084
## - EducationFieldMarketing           1    2099873 1273275343 10084
## - PercentSalaryHike                 1    2169855 1273345326 10084
## - EnvironmentSatisfaction           1    3224557 1274400028 10085
## <none>                                           1271175471 10085
## - YearsWithCurrManager              1    9558249 1280733720 10088
## - BusinessTravelTravel_Rarely       1   16806021 1287981492 10092
## - DistanceFromHome                  1   17867760 1289043231 10092
## - TotalWorkingYears                 1   51538196 1322713666 10110
## - JobLevel                          1 4363621804 5634797275 11119
## 
## Step:  AIC=10082.85
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + Education + 
##     `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     JobSatisfaction + MaritalStatusMarried + MaritalStatusSingle + 
##     NumCompaniesWorked + PercentSalaryHike + RelationshipSatisfaction + 
##     TotalWorkingYears + WorkLifeBalance + YearsInCurrentRole + 
##     YearsSinceLastPromotion + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - NumCompaniesWorked                1     167703 1271371912 10081
## - GenderMale                        1     195255 1271399464 10081
## - EducationFieldMedical             1     219259 1271423468 10081
## - EducationFieldOther               1     244490 1271448699 10081
## - MaritalStatusSingle               1     260384 1271464593 10081
## - `EducationFieldTechnical Degree`  1     266453 1271470662 10081
## - RelationshipSatisfaction          1     282459 1271486668 10081
## - `EducationFieldLife Sciences`     1     285952 1271490161 10081
## - WorkLifeBalance                   1     286779 1271490988 10081
## - Education                         1     317046 1271521255 10081
## - JobSatisfaction                   1     679702 1271883911 10081
## - YearsInCurrentRole                1     891795 1272096004 10081
## - YearsSinceLastPromotion           1    1017043 1272221252 10081
## - JobInvolvement                    1    1042914 1272247123 10081
## - MaritalStatusMarried              1    1833651 1273037860 10082
## - EducationFieldMarketing           1    2084914 1273289123 10082
## - PercentSalaryHike                 1    2159774 1273363982 10082
## - EnvironmentSatisfaction           1    3240847 1274445056 10083
## <none>                                           1271204209 10083
## - YearsWithCurrManager              1    9544836 1280749045 10086
## - BusinessTravelTravel_Rarely       1   16781475 1287985683 10090
## - DistanceFromHome                  1   18027264 1289231473 10091
## - TotalWorkingYears                 1   51644816 1322849024 10109
## - JobLevel                          1 4367908809 5639113018 11118
## 
## Step:  AIC=10080.94
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + Education + 
##     `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     JobSatisfaction + MaritalStatusMarried + MaritalStatusSingle + 
##     PercentSalaryHike + RelationshipSatisfaction + TotalWorkingYears + 
##     WorkLifeBalance + YearsInCurrentRole + YearsSinceLastPromotion + 
##     YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - GenderMale                        1     201687 1271573599 10079
## - EducationFieldMedical             1     230090 1271602002 10079
## - EducationFieldOther               1     247359 1271619271 10079
## - Education                         1     250359 1271622271 10079
## - RelationshipSatisfaction          1     255689 1271627601 10079
## - MaritalStatusSingle               1     265511 1271637423 10079
## - `EducationFieldTechnical Degree`  1     279797 1271651709 10079
## - `EducationFieldLife Sciences`     1     298959 1271670871 10079
## - WorkLifeBalance                   1     302084 1271673995 10079
## - JobSatisfaction                   1     637206 1272009118 10079
## - YearsInCurrentRole                1     842404 1272214316 10079
## - JobInvolvement                    1    1025568 1272397480 10080
## - YearsSinceLastPromotion           1    1139057 1272510968 10080
## - MaritalStatusMarried              1    1845446 1273217358 10080
## - EducationFieldMarketing           1    2117921 1273489833 10080
## - PercentSalaryHike                 1    2165850 1273537762 10080
## - EnvironmentSatisfaction           1    3299638 1274671550 10081
## <none>                                           1271371912 10081
## - YearsWithCurrManager              1    9377969 1280749881 10084
## - BusinessTravelTravel_Rarely       1   16771441 1288143353 10088
## - DistanceFromHome                  1   17863277 1289235189 10089
## - TotalWorkingYears                 1   55950314 1327322226 10109
## - JobLevel                          1 4425564485 5696936397 11123
## 
## Step:  AIC=10079.05
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + Education + 
##     `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + JobInvolvement + JobLevel + JobSatisfaction + 
##     MaritalStatusMarried + MaritalStatusSingle + PercentSalaryHike + 
##     RelationshipSatisfaction + TotalWorkingYears + WorkLifeBalance + 
##     YearsInCurrentRole + YearsSinceLastPromotion + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - EducationFieldMedical             1     251162 1271824761 10077
## - EducationFieldOther               1     257294 1271830892 10077
## - Education                         1     264942 1271838541 10077
## - RelationshipSatisfaction          1     280428 1271854026 10077
## - `EducationFieldTechnical Degree`  1     301201 1271874799 10077
## - MaritalStatusSingle               1     306049 1271879648 10077
## - WorkLifeBalance                   1     310881 1271884480 10077
## - `EducationFieldLife Sciences`     1     321844 1271895443 10077
## - JobSatisfaction                   1     618787 1272192385 10077
## - YearsInCurrentRole                1     855242 1272428841 10078
## - JobInvolvement                    1    1041817 1272615415 10078
## - YearsSinceLastPromotion           1    1135329 1272708928 10078
## - MaritalStatusMarried              1    1777675 1273351274 10078
## - PercentSalaryHike                 1    2160321 1273733919 10078
## - EducationFieldMarketing           1    2210524 1273784122 10078
## - EnvironmentSatisfaction           1    3267033 1274840631 10079
## <none>                                           1271573599 10079
## - YearsWithCurrManager              1    9492176 1281065775 10082
## - BusinessTravelTravel_Rarely       1   16876123 1288449721 10086
## - DistanceFromHome                  1   17933396 1289506995 10087
## - TotalWorkingYears                 1   55944592 1327518190 10107
## - JobLevel                          1 4425730623 5697304222 11121
## 
## Step:  AIC=10077.18
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + Education + 
##     `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + JobInvolvement + JobLevel + JobSatisfaction + 
##     MaritalStatusMarried + MaritalStatusSingle + PercentSalaryHike + 
##     RelationshipSatisfaction + TotalWorkingYears + WorkLifeBalance + 
##     YearsInCurrentRole + YearsSinceLastPromotion + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - EducationFieldOther               1      26490 1271851252 10075
## - `EducationFieldTechnical Degree`  1      52083 1271876844 10075
## - `EducationFieldLife Sciences`     1      83029 1271907790 10075
## - Education                         1     279031 1272103792 10075
## - WorkLifeBalance                   1     290315 1272115076 10075
## - RelationshipSatisfaction          1     298976 1272123737 10075
## - MaritalStatusSingle               1     325107 1272149868 10075
## - JobSatisfaction                   1     638135 1272462896 10076
## - YearsInCurrentRole                1     864742 1272689504 10076
## - JobInvolvement                    1    1022901 1272847662 10076
## - YearsSinceLastPromotion           1    1096074 1272920835 10076
## - MaritalStatusMarried              1    1842431 1273667192 10076
## - PercentSalaryHike                 1    2133802 1273958563 10076
## - EnvironmentSatisfaction           1    3241682 1275066443 10077
## <none>                                           1271824761 10077
## - EducationFieldMarketing           1    5122262 1276947023 10078
## - YearsWithCurrManager              1    9572859 1281397620 10080
## - BusinessTravelTravel_Rarely       1   16814668 1288639429 10084
## - DistanceFromHome                  1   18065090 1289889851 10085
## - TotalWorkingYears                 1   55972380 1327797141 10105
## - JobLevel                          1 4426268282 5698093043 11119
## 
## Step:  AIC=10075.2
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + Education + 
##     `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     JobInvolvement + JobLevel + JobSatisfaction + MaritalStatusMarried + 
##     MaritalStatusSingle + PercentSalaryHike + RelationshipSatisfaction + 
##     TotalWorkingYears + WorkLifeBalance + YearsInCurrentRole + 
##     YearsSinceLastPromotion + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - `EducationFieldTechnical Degree`  1      40887 1271892138 10073
## - `EducationFieldLife Sciences`     1      63394 1271914646 10073
## - Education                         1     269712 1272120963 10073
## - WorkLifeBalance                   1     291244 1272142496 10073
## - RelationshipSatisfaction          1     305746 1272156997 10073
## - MaritalStatusSingle               1     329632 1272180884 10073
## - JobSatisfaction                   1     632406 1272483658 10074
## - YearsInCurrentRole                1     867823 1272719074 10074
## - JobInvolvement                    1    1057345 1272908597 10074
## - YearsSinceLastPromotion           1    1109805 1272961057 10074
## - MaritalStatusMarried              1    1829435 1273680686 10074
## - PercentSalaryHike                 1    2126348 1273977600 10074
## - EnvironmentSatisfaction           1    3309770 1275161022 10075
## <none>                                           1271851252 10075
## - EducationFieldMarketing           1    5184948 1277036199 10076
## - YearsWithCurrManager              1    9581144 1281432395 10078
## - BusinessTravelTravel_Rarely       1   16815659 1288666910 10082
## - DistanceFromHome                  1   18066747 1289917999 10083
## - TotalWorkingYears                 1   56416671 1328267923 10103
## - JobLevel                          1 4429073160 5700924411 11117
## 
## Step:  AIC=10073.22
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + Education + 
##     `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EnvironmentSatisfaction + JobInvolvement + JobLevel + JobSatisfaction + 
##     MaritalStatusMarried + MaritalStatusSingle + PercentSalaryHike + 
##     RelationshipSatisfaction + TotalWorkingYears + WorkLifeBalance + 
##     YearsInCurrentRole + YearsSinceLastPromotion + YearsWithCurrManager
## 
##                                 Df  Sum of Sq        RSS   AIC
## - `EducationFieldLife Sciences`  1      39712 1271931850 10071
## - Education                      1     267170 1272159308 10071
## - WorkLifeBalance                1     304510 1272196648 10071
## - RelationshipSatisfaction       1     315821 1272207960 10071
## - MaritalStatusSingle            1     332562 1272224700 10071
## - JobSatisfaction                1     619545 1272511683 10072
## - YearsInCurrentRole             1     874164 1272766302 10072
## - JobInvolvement                 1    1052372 1272944510 10072
## - YearsSinceLastPromotion        1    1089186 1272981325 10072
## - MaritalStatusMarried           1    1812579 1273704718 10072
## - PercentSalaryHike              1    2196039 1274088177 10072
## - EnvironmentSatisfaction        1    3308440 1275200579 10073
## <none>                                        1271892138 10073
## - EducationFieldMarketing        1    5208597 1277100735 10074
## - YearsWithCurrManager           1    9551783 1281443921 10076
## - BusinessTravelTravel_Rarely    1   16792409 1288684547 10080
## - DistanceFromHome               1   18045797 1289937935 10081
## - TotalWorkingYears              1   56728168 1328620306 10102
## - JobLevel                       1 4429508525 5701400663 11115
## 
## Step:  AIC=10071.24
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + Education + 
##     EducationFieldMarketing + EnvironmentSatisfaction + JobInvolvement + 
##     JobLevel + JobSatisfaction + MaritalStatusMarried + MaritalStatusSingle + 
##     PercentSalaryHike + RelationshipSatisfaction + TotalWorkingYears + 
##     WorkLifeBalance + YearsInCurrentRole + YearsSinceLastPromotion + 
##     YearsWithCurrManager
## 
##                               Df  Sum of Sq        RSS   AIC
## - Education                    1     261293 1272193143 10069
## - WorkLifeBalance              1     301784 1272233634 10069
## - RelationshipSatisfaction     1     323628 1272255478 10069
## - MaritalStatusSingle          1     338508 1272270358 10069
## - JobSatisfaction              1     623469 1272555319 10070
## - YearsInCurrentRole           1     883233 1272815083 10070
## - JobInvolvement               1    1050730 1272982580 10070
## - YearsSinceLastPromotion      1    1104033 1273035883 10070
## - MaritalStatusMarried         1    1818992 1273750842 10070
## - PercentSalaryHike            1    2180207 1274112057 10070
## - EnvironmentSatisfaction      1    3298812 1275230662 10071
## <none>                                      1271931850 10071
## - EducationFieldMarketing      1    5412794 1277344644 10072
## - YearsWithCurrManager         1    9532644 1281464494 10074
## - BusinessTravelTravel_Rarely  1   16752858 1288684708 10078
## - DistanceFromHome             1   18020408 1289952258 10079
## - TotalWorkingYears            1   56786700 1328718550 10100
## - JobLevel                     1 4435505130 5707436980 11114
## 
## Step:  AIC=10069.39
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + EducationFieldMarketing + 
##     EnvironmentSatisfaction + JobInvolvement + JobLevel + JobSatisfaction + 
##     MaritalStatusMarried + MaritalStatusSingle + PercentSalaryHike + 
##     RelationshipSatisfaction + TotalWorkingYears + WorkLifeBalance + 
##     YearsInCurrentRole + YearsSinceLastPromotion + YearsWithCurrManager
## 
##                               Df  Sum of Sq        RSS   AIC
## - WorkLifeBalance              1     281348 1272474491 10068
## - RelationshipSatisfaction     1     306670 1272499813 10068
## - MaritalStatusSingle          1     349705 1272542848 10068
## - JobSatisfaction              1     617692 1272810835 10068
## - YearsInCurrentRole           1     903611 1273096754 10068
## - JobInvolvement               1    1114047 1273307190 10068
## - YearsSinceLastPromotion      1    1135778 1273328921 10068
## - MaritalStatusMarried         1    1776593 1273969736 10068
## - PercentSalaryHike            1    2239092 1274432235 10069
## - EnvironmentSatisfaction      1    3307110 1275500252 10069
## <none>                                      1272193143 10069
## - EducationFieldMarketing      1    5269299 1277462442 10070
## - YearsWithCurrManager         1    9457426 1281650569 10072
## - BusinessTravelTravel_Rarely  1   16773910 1288967053 10076
## - DistanceFromHome             1   17828345 1290021488 10077
## - TotalWorkingYears            1   57641127 1329834270 10098
## - JobLevel                     1 4438422819 5710615962 11112
## 
## Step:  AIC=10067.54
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + EducationFieldMarketing + 
##     EnvironmentSatisfaction + JobInvolvement + JobLevel + JobSatisfaction + 
##     MaritalStatusMarried + MaritalStatusSingle + PercentSalaryHike + 
##     RelationshipSatisfaction + TotalWorkingYears + YearsInCurrentRole + 
##     YearsSinceLastPromotion + YearsWithCurrManager
## 
##                               Df  Sum of Sq        RSS   AIC
## - RelationshipSatisfaction     1     287771 1272762262 10066
## - MaritalStatusSingle          1     351734 1272826226 10066
## - JobSatisfaction              1     619072 1273093563 10066
## - YearsInCurrentRole           1    1012973 1273487464 10066
## - JobInvolvement               1    1103370 1273577861 10066
## - YearsSinceLastPromotion      1    1121000 1273595491 10066
## - MaritalStatusMarried         1    1801696 1274276188 10066
## - PercentSalaryHike            1    2264564 1274739055 10067
## - EnvironmentSatisfaction      1    3518029 1275992520 10068
## <none>                                      1272474491 10068
## - EducationFieldMarketing      1    5338524 1277813015 10068
## - YearsWithCurrManager         1    9281840 1281756331 10071
## - BusinessTravelTravel_Rarely  1   17016907 1289491398 10075
## - DistanceFromHome             1   17727831 1290202322 10075
## - TotalWorkingYears            1   57836250 1330310741 10096
## - JobLevel                     1 4440218490 5712692981 11111
## 
## Step:  AIC=10065.7
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + EducationFieldMarketing + 
##     EnvironmentSatisfaction + JobInvolvement + JobLevel + JobSatisfaction + 
##     MaritalStatusMarried + MaritalStatusSingle + PercentSalaryHike + 
##     TotalWorkingYears + YearsInCurrentRole + YearsSinceLastPromotion + 
##     YearsWithCurrManager
## 
##                               Df  Sum of Sq        RSS   AIC
## - MaritalStatusSingle          1     331235 1273093497 10064
## - JobSatisfaction              1     654951 1273417213 10064
## - YearsInCurrentRole           1    1005383 1273767645 10064
## - JobInvolvement               1    1131311 1273893573 10064
## - YearsSinceLastPromotion      1    1203817 1273966078 10064
## - MaritalStatusMarried         1    1740896 1274503158 10065
## - PercentSalaryHike            1    2175195 1274937457 10065
## - EnvironmentSatisfaction      1    3514953 1276277215 10066
## <none>                                      1272762262 10066
## - EducationFieldMarketing      1    5250191 1278012453 10067
## - YearsWithCurrManager         1    9429243 1282191505 10069
## - BusinessTravelTravel_Rarely  1   16851276 1289613538 10073
## - DistanceFromHome             1   17574086 1290336348 10073
## - TotalWorkingYears            1   57629064 1330391326 10094
## - JobLevel                     1 4441460476 5714222738 11109
## 
## Step:  AIC=10063.88
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + EducationFieldMarketing + 
##     EnvironmentSatisfaction + JobInvolvement + JobLevel + JobSatisfaction + 
##     MaritalStatusMarried + PercentSalaryHike + TotalWorkingYears + 
##     YearsInCurrentRole + YearsSinceLastPromotion + YearsWithCurrManager
## 
##                               Df  Sum of Sq        RSS   AIC
## - JobSatisfaction              1     674850 1273768347 10062
## - YearsInCurrentRole           1     938525 1274032022 10062
## - YearsSinceLastPromotion      1    1163924 1274257421 10062
## - JobInvolvement               1    1230519 1274324016 10063
## - PercentSalaryHike            1    2205393 1275298890 10063
## - EnvironmentSatisfaction      1    3445314 1276538811 10064
## <none>                                      1273093497 10064
## - MaritalStatusMarried         1    4634335 1277727832 10064
## - EducationFieldMarketing      1    5239906 1278333403 10065
## - YearsWithCurrManager         1    9688837 1282782334 10067
## - BusinessTravelTravel_Rarely  1   16811486 1289904983 10071
## - DistanceFromHome             1   17463386 1290556883 10071
## - TotalWorkingYears            1   58424750 1331518247 10093
## - JobLevel                     1 4441139423 5714232920 11107
## 
## Step:  AIC=10062.25
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + EducationFieldMarketing + 
##     EnvironmentSatisfaction + JobInvolvement + JobLevel + MaritalStatusMarried + 
##     PercentSalaryHike + TotalWorkingYears + YearsInCurrentRole + 
##     YearsSinceLastPromotion + YearsWithCurrManager
## 
##                               Df  Sum of Sq        RSS   AIC
## - YearsInCurrentRole           1     954381 1274722728 10061
## - YearsSinceLastPromotion      1    1236006 1275004353 10061
## - JobInvolvement               1    1327276 1275095623 10061
## - PercentSalaryHike            1    2178202 1275946549 10061
## - EnvironmentSatisfaction      1    3373293 1277141640 10062
## <none>                                      1273768347 10062
## - MaritalStatusMarried         1    4584916 1278353263 10063
## - EducationFieldMarketing      1    5166237 1278934583 10063
## - YearsWithCurrManager         1    9874684 1283643031 10066
## - DistanceFromHome             1   17278008 1291046355 10070
## - BusinessTravelTravel_Rarely  1   17631335 1291399681 10070
## - TotalWorkingYears            1   58529691 1332298038 10092
## - JobLevel                     1 4443084957 5716853304 11105
## 
## Step:  AIC=10060.77
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + EducationFieldMarketing + 
##     EnvironmentSatisfaction + JobInvolvement + JobLevel + MaritalStatusMarried + 
##     PercentSalaryHike + TotalWorkingYears + YearsSinceLastPromotion + 
##     YearsWithCurrManager
## 
##                               Df  Sum of Sq        RSS   AIC
## - YearsSinceLastPromotion      1     797581 1275520309 10059
## - JobInvolvement               1    1267170 1275989898 10060
## - PercentSalaryHike            1    2065852 1276788580 10060
## - EnvironmentSatisfaction      1    3554724 1278277452 10061
## <none>                                      1274722728 10061
## - MaritalStatusMarried         1    4207130 1278929858 10061
## - EducationFieldMarketing      1    5210326 1279933053 10062
## - BusinessTravelTravel_Rarely  1   17208245 1291930972 10068
## - DistanceFromHome             1   17264275 1291987003 10068
## - YearsWithCurrManager         1   19854057 1294576785 10070
## - TotalWorkingYears            1   57600083 1332322811 10090
## - JobLevel                     1 4442874412 5717597140 11103
## 
## Step:  AIC=10059.2
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + EducationFieldMarketing + 
##     EnvironmentSatisfaction + JobInvolvement + JobLevel + MaritalStatusMarried + 
##     PercentSalaryHike + TotalWorkingYears + YearsWithCurrManager
## 
##                               Df  Sum of Sq        RSS   AIC
## - JobInvolvement               1    1191411 1276711720 10058
## - PercentSalaryHike            1    1991671 1277511980 10058
## - EnvironmentSatisfaction      1    3447755 1278968064 10059
## <none>                                      1275520309 10059
## - MaritalStatusMarried         1    4487023 1280007332 10060
## - EducationFieldMarketing      1    5200173 1280720482 10060
## - BusinessTravelTravel_Rarely  1   16806203 1292326512 10066
## - DistanceFromHome             1   17288672 1292808981 10067
## - YearsWithCurrManager         1   19928025 1295448334 10068
## - TotalWorkingYears            1   63667867 1339188176 10091
## - JobLevel                     1 4451080064 5726600373 11102
## 
## Step:  AIC=10057.85
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + EducationFieldMarketing + 
##     EnvironmentSatisfaction + JobLevel + MaritalStatusMarried + 
##     PercentSalaryHike + TotalWorkingYears + YearsWithCurrManager
## 
##                               Df  Sum of Sq        RSS   AIC
## - PercentSalaryHike            1    2064655 1278776375 10057
## - EnvironmentSatisfaction      1    3375718 1280087438 10058
## <none>                                      1276711720 10058
## - MaritalStatusMarried         1    4594646 1281306365 10058
## - EducationFieldMarketing      1    5500720 1282212439 10059
## - BusinessTravelTravel_Rarely  1   17105888 1293817608 10065
## - DistanceFromHome             1   17367690 1294079410 10065
## - YearsWithCurrManager         1   19933300 1296645020 10067
## - TotalWorkingYears            1   63319476 1340031195 10090
## - JobLevel                     1 4451873471 5728585191 11101
## 
## Step:  AIC=10056.98
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + EducationFieldMarketing + 
##     EnvironmentSatisfaction + JobLevel + MaritalStatusMarried + 
##     TotalWorkingYears + YearsWithCurrManager
## 
##                               Df  Sum of Sq        RSS   AIC
## - EnvironmentSatisfaction      1    3500546 1282276921 10057
## <none>                                      1278776375 10057
## - MaritalStatusMarried         1    4460390 1283236765 10057
## - EducationFieldMarketing      1    5719813 1284496188 10058
## - DistanceFromHome             1   16810273 1295586648 10064
## - BusinessTravelTravel_Rarely  1   17018007 1295794383 10064
## - YearsWithCurrManager         1   20053204 1298829579 10066
## - TotalWorkingYears            1   62857883 1341634258 10088
## - JobLevel                     1 4449924933 5728701308 11099
## 
## Step:  AIC=10056.88
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + EducationFieldMarketing + 
##     JobLevel + MaritalStatusMarried + TotalWorkingYears + YearsWithCurrManager
## 
##                               Df  Sum of Sq        RSS   AIC
## <none>                                      1282276921 10057
## - MaritalStatusMarried         1    4685600 1286962521 10057
## - EducationFieldMarketing      1    5828784 1288105705 10058
## - DistanceFromHome             1   16540804 1298817725 10064
## - BusinessTravelTravel_Rarely  1   17920392 1300197314 10064
## - YearsWithCurrManager         1   20079302 1302356223 10066
## - TotalWorkingYears            1   63603811 1345880732 10089
## - JobLevel                     1 4447458584 5729735505 11097
## Start:  AIC=10076.36
## .outcome ~ Age + BusinessTravelTravel_Frequently + BusinessTravelTravel_Rarely + 
##     DistanceFromHome + Education + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     GenderMale + JobInvolvement + JobLevel + JobSatisfaction + 
##     MaritalStatusMarried + MaritalStatusSingle + NumCompaniesWorked + 
##     OverTimeYes + PercentSalaryHike + RelationshipSatisfaction + 
##     StockOptionLevel + TotalWorkingYears + TrainingTimesLastYear + 
##     WorkLifeBalance + YearsInCurrentRole + YearsSinceLastPromotion + 
##     YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - YearsSinceLastPromotion           1        406 1265615138 10074
## - RelationshipSatisfaction          1       1944 1265616676 10074
## - GenderMale                        1      23648 1265638380 10074
## - StockOptionLevel                  1      32080 1265646812 10074
## - BusinessTravelTravel_Frequently   1     158974 1265773707 10074
## - OverTimeYes                       1     170540 1265785272 10074
## - MaritalStatusSingle               1     187647 1265802380 10074
## - PercentSalaryHike                 1     597137 1266211869 10075
## - JobSatisfaction                   1     600205 1266214938 10075
## - YearsInCurrentRole                1     724875 1266339608 10075
## - Education                         1     783092 1266397825 10075
## - WorkLifeBalance                   1     846836 1266461569 10075
## - Age                               1     929169 1266543901 10075
## - TrainingTimesLastYear             1    1356882 1266971615 10075
## - EducationFieldOther               1    1420963 1267035696 10075
## - `EducationFieldLife Sciences`     1    1434302 1267049035 10075
## - EducationFieldMedical             1    1496737 1267111470 10075
## - `EducationFieldTechnical Degree`  1    1855113 1267469846 10075
## - BusinessTravelTravel_Rarely       1    1998688 1267613420 10076
## - JobInvolvement                    1    3074263 1268688996 10076
## <none>                                           1265614733 10076
## - EnvironmentSatisfaction           1    3704100 1269318832 10076
## - MaritalStatusMarried              1    3748367 1269363100 10076
## - NumCompaniesWorked                1    3828399 1269443132 10076
## - EducationFieldMarketing           1    7209394 1272824126 10078
## - YearsWithCurrManager              1   12193059 1277807792 10081
## - DistanceFromHome                  1   12463632 1278078364 10081
## - TotalWorkingYears                 1   46935301 1312550033 10100
## - JobLevel                          1 4324366466 5589981199 11107
## 
## Step:  AIC=10074.36
## .outcome ~ Age + BusinessTravelTravel_Frequently + BusinessTravelTravel_Rarely + 
##     DistanceFromHome + Education + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     GenderMale + JobInvolvement + JobLevel + JobSatisfaction + 
##     MaritalStatusMarried + MaritalStatusSingle + NumCompaniesWorked + 
##     OverTimeYes + PercentSalaryHike + RelationshipSatisfaction + 
##     StockOptionLevel + TotalWorkingYears + TrainingTimesLastYear + 
##     WorkLifeBalance + YearsInCurrentRole + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - RelationshipSatisfaction          1       1872 1265617010 10072
## - GenderMale                        1      24384 1265639522 10072
## - StockOptionLevel                  1      31951 1265647090 10072
## - BusinessTravelTravel_Frequently   1     158705 1265773843 10072
## - OverTimeYes                       1     170515 1265785653 10072
## - MaritalStatusSingle               1     187401 1265802540 10072
## - PercentSalaryHike                 1     596731 1266211869 10073
## - JobSatisfaction                   1     603323 1266218461 10073
## - YearsInCurrentRole                1     782115 1266397254 10073
## - Education                         1     784527 1266399665 10073
## - WorkLifeBalance                   1     847126 1266462265 10073
## - Age                               1     932740 1266547879 10073
## - TrainingTimesLastYear             1    1368533 1266983672 10073
## - EducationFieldOther               1    1421608 1267036747 10073
## - `EducationFieldLife Sciences`     1    1433922 1267049060 10073
## - EducationFieldMedical             1    1496422 1267111561 10073
## - `EducationFieldTechnical Degree`  1    1855288 1267470426 10073
## - BusinessTravelTravel_Rarely       1    2000277 1267615416 10074
## - JobInvolvement                    1    3074807 1268689946 10074
## <none>                                           1265615138 10074
## - EnvironmentSatisfaction           1    3704132 1269319270 10074
## - MaritalStatusMarried              1    3748502 1269363641 10074
## - NumCompaniesWorked                1    3876869 1269492007 10074
## - EducationFieldMarketing           1    7211418 1272826556 10076
## - YearsWithCurrManager              1   12302982 1277918120 10079
## - DistanceFromHome                  1   12503206 1278118345 10079
## - TotalWorkingYears                 1   48932159 1314547297 10099
## - JobLevel                          1 4334900943 5600516081 11106
## 
## Step:  AIC=10072.37
## .outcome ~ Age + BusinessTravelTravel_Frequently + BusinessTravelTravel_Rarely + 
##     DistanceFromHome + Education + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     GenderMale + JobInvolvement + JobLevel + JobSatisfaction + 
##     MaritalStatusMarried + MaritalStatusSingle + NumCompaniesWorked + 
##     OverTimeYes + PercentSalaryHike + StockOptionLevel + TotalWorkingYears + 
##     TrainingTimesLastYear + WorkLifeBalance + YearsInCurrentRole + 
##     YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - GenderMale                        1      24277 1265641287 10070
## - StockOptionLevel                  1      32040 1265649051 10070
## - BusinessTravelTravel_Frequently   1     159716 1265776726 10070
## - OverTimeYes                       1     172677 1265789687 10070
## - MaritalStatusSingle               1     187631 1265804641 10070
## - PercentSalaryHike                 1     598849 1266215859 10071
## - JobSatisfaction                   1     602166 1266219176 10071
## - YearsInCurrentRole                1     783492 1266400503 10071
## - Education                         1     786306 1266403316 10071
## - WorkLifeBalance                   1     851563 1266468573 10071
## - Age                               1     932376 1266549386 10071
## - TrainingTimesLastYear             1    1366896 1266983906 10071
## - EducationFieldOther               1    1419745 1267036756 10071
## - `EducationFieldLife Sciences`     1    1432101 1267049112 10071
## - EducationFieldMedical             1    1494897 1267111907 10071
## - `EducationFieldTechnical Degree`  1    1853474 1267470484 10071
## - BusinessTravelTravel_Rarely       1    2006141 1267623151 10072
## - JobInvolvement                    1    3073213 1268690223 10072
## <none>                                           1265617010 10072
## - EnvironmentSatisfaction           1    3708299 1269325309 10072
## - MaritalStatusMarried              1    3763538 1269380548 10072
## - NumCompaniesWorked                1    3882269 1269499280 10072
## - EducationFieldMarketing           1    7213426 1272830436 10074
## - YearsWithCurrManager              1   12301458 1277918468 10077
## - DistanceFromHome                  1   12539802 1278156812 10077
## - TotalWorkingYears                 1   48971333 1314588343 10097
## - JobLevel                          1 4341196625 5606813635 11105
## 
## Step:  AIC=10070.38
## .outcome ~ Age + BusinessTravelTravel_Frequently + BusinessTravelTravel_Rarely + 
##     DistanceFromHome + Education + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     JobInvolvement + JobLevel + JobSatisfaction + MaritalStatusMarried + 
##     MaritalStatusSingle + NumCompaniesWorked + OverTimeYes + 
##     PercentSalaryHike + StockOptionLevel + TotalWorkingYears + 
##     TrainingTimesLastYear + WorkLifeBalance + YearsInCurrentRole + 
##     YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - StockOptionLevel                  1      29529 1265670815 10068
## - BusinessTravelTravel_Frequently   1     166310 1265807597 10068
## - OverTimeYes                       1     172712 1265813998 10068
## - MaritalStatusSingle               1     174574 1265815860 10068
## - PercentSalaryHike                 1     593405 1266234691 10069
## - JobSatisfaction                   1     599634 1266240921 10069
## - YearsInCurrentRole                1     797096 1266438383 10069
## - Education                         1     805476 1266446763 10069
## - WorkLifeBalance                   1     865448 1266506735 10069
## - Age                               1     952781 1266594068 10069
## - TrainingTimesLastYear             1    1377270 1267018557 10069
## - EducationFieldOther               1    1416709 1267057996 10069
## - `EducationFieldLife Sciences`     1    1432473 1267073760 10069
## - EducationFieldMedical             1    1495377 1267136664 10069
## - `EducationFieldTechnical Degree`  1    1861972 1267503259 10069
## - BusinessTravelTravel_Rarely       1    1995258 1267636545 10070
## - JobInvolvement                    1    3086217 1268727504 10070
## <none>                                           1265641287 10070
## - EnvironmentSatisfaction           1    3688986 1269330273 10070
## - MaritalStatusMarried              1    3748877 1269390164 10070
## - NumCompaniesWorked                1    3895772 1269537059 10070
## - EducationFieldMarketing           1    7236924 1272878211 10072
## - YearsWithCurrManager              1   12368928 1278010215 10075
## - DistanceFromHome                  1   12519540 1278160826 10075
## - TotalWorkingYears                 1   49205468 1314846755 10095
## - JobLevel                          1 4351954174 5617595461 11104
## 
## Step:  AIC=10068.39
## .outcome ~ Age + BusinessTravelTravel_Frequently + BusinessTravelTravel_Rarely + 
##     DistanceFromHome + Education + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     JobInvolvement + JobLevel + JobSatisfaction + MaritalStatusMarried + 
##     MaritalStatusSingle + NumCompaniesWorked + OverTimeYes + 
##     PercentSalaryHike + TotalWorkingYears + TrainingTimesLastYear + 
##     WorkLifeBalance + YearsInCurrentRole + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - MaritalStatusSingle               1     162542 1265833357 10066
## - BusinessTravelTravel_Frequently   1     162760 1265833575 10066
## - OverTimeYes                       1     175849 1265846664 10066
## - PercentSalaryHike                 1     588993 1266259809 10067
## - JobSatisfaction                   1     594257 1266265072 10067
## - YearsInCurrentRole                1     787027 1266457842 10067
## - Education                         1     815791 1266486606 10067
## - WorkLifeBalance                   1     850714 1266521529 10067
## - Age                               1     961819 1266632634 10067
## - TrainingTimesLastYear             1    1410070 1267080885 10067
## - EducationFieldOther               1    1413840 1267084655 10067
## - `EducationFieldLife Sciences`     1    1418663 1267089478 10067
## - EducationFieldMedical             1    1479153 1267149969 10067
## - `EducationFieldTechnical Degree`  1    1848598 1267519413 10067
## - BusinessTravelTravel_Rarely       1    1991860 1267662675 10068
## - JobInvolvement                    1    3098735 1268769551 10068
## <none>                                           1265670815 10068
## - EnvironmentSatisfaction           1    3679864 1269350679 10068
## - NumCompaniesWorked                1    3866285 1269537101 10068
## - MaritalStatusMarried              1    3996368 1269667183 10069
## - EducationFieldMarketing           1    7208965 1272879781 10070
## - YearsWithCurrManager              1   12339600 1278010415 10073
## - DistanceFromHome                  1   12494482 1278165297 10073
## - TotalWorkingYears                 1   49208068 1314878883 10093
## - JobLevel                          1 4370898180 5636568995 11104
## 
## Step:  AIC=10066.48
## .outcome ~ Age + BusinessTravelTravel_Frequently + BusinessTravelTravel_Rarely + 
##     DistanceFromHome + Education + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     JobInvolvement + JobLevel + JobSatisfaction + MaritalStatusMarried + 
##     NumCompaniesWorked + OverTimeYes + PercentSalaryHike + TotalWorkingYears + 
##     TrainingTimesLastYear + WorkLifeBalance + YearsInCurrentRole + 
##     YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - BusinessTravelTravel_Frequently   1     127365 1265960722 10065
## - OverTimeYes                       1     178556 1266011913 10065
## - JobSatisfaction                   1     574784 1266408141 10065
## - PercentSalaryHike                 1     599131 1266432488 10065
## - Education                         1     815173 1266648530 10065
## - YearsInCurrentRole                1     818868 1266652225 10065
## - WorkLifeBalance                   1     863301 1266696658 10065
## - Age                               1     990050 1266823407 10065
## - EducationFieldOther               1    1376577 1267209934 10065
## - `EducationFieldLife Sciences`     1    1379682 1267213040 10065
## - TrainingTimesLastYear             1    1430773 1267264130 10065
## - EducationFieldMedical             1    1448845 1267282202 10065
## - `EducationFieldTechnical Degree`  1    1812748 1267646105 10066
## - BusinessTravelTravel_Rarely       1    2076234 1267909591 10066
## - JobInvolvement                    1    3007271 1268840628 10066
## <none>                                           1265833357 10066
## - EnvironmentSatisfaction           1    3678291 1269511649 10066
## - NumCompaniesWorked                1    3857927 1269691284 10067
## - MaritalStatusMarried              1    5037790 1270871147 10067
## - EducationFieldMarketing           1    7132888 1272966245 10068
## - YearsWithCurrManager              1   12277908 1278111265 10071
## - DistanceFromHome                  1   12451064 1278284421 10071
## - TotalWorkingYears                 1   49243955 1315077313 10091
## - JobLevel                          1 4374771775 5640605133 11103
## 
## Step:  AIC=10064.55
## .outcome ~ Age + BusinessTravelTravel_Rarely + DistanceFromHome + 
##     Education + `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + JobInvolvement + JobLevel + JobSatisfaction + 
##     MaritalStatusMarried + NumCompaniesWorked + OverTimeYes + 
##     PercentSalaryHike + TotalWorkingYears + TrainingTimesLastYear + 
##     WorkLifeBalance + YearsInCurrentRole + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - OverTimeYes                       1     190826 1266151548 10063
## - JobSatisfaction                   1     590015 1266550736 10063
## - PercentSalaryHike                 1     637020 1266597742 10063
## - Education                         1     817105 1266777827 10063
## - YearsInCurrentRole                1     835635 1266796357 10063
## - WorkLifeBalance                   1     863227 1266823949 10063
## - Age                               1     969656 1266930378 10063
## - TrainingTimesLastYear             1    1372751 1267333473 10063
## - EducationFieldOther               1    1376006 1267336728 10063
## - `EducationFieldLife Sciences`     1    1385135 1267345857 10063
## - EducationFieldMedical             1    1445690 1267406412 10063
## - `EducationFieldTechnical Degree`  1    1807618 1267768339 10064
## - JobInvolvement                    1    2956537 1268917259 10064
## <none>                                           1265960722 10065
## - EnvironmentSatisfaction           1    3716615 1269677337 10065
## - NumCompaniesWorked                1    3796869 1269757590 10065
## - MaritalStatusMarried              1    5004662 1270965384 10065
## - BusinessTravelTravel_Rarely       1    6727498 1272688220 10066
## - EducationFieldMarketing           1    7126380 1273087102 10066
## - YearsWithCurrManager              1   12197567 1278158289 10069
## - DistanceFromHome                  1   12427003 1278387725 10069
## - TotalWorkingYears                 1   49130988 1315091710 10089
## - JobLevel                          1 4380185100 5646145821 11102
## 
## Step:  AIC=10062.66
## .outcome ~ Age + BusinessTravelTravel_Rarely + DistanceFromHome + 
##     Education + `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + JobInvolvement + JobLevel + JobSatisfaction + 
##     MaritalStatusMarried + NumCompaniesWorked + PercentSalaryHike + 
##     TotalWorkingYears + TrainingTimesLastYear + WorkLifeBalance + 
##     YearsInCurrentRole + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - JobSatisfaction                   1     598229 1266749777 10061
## - PercentSalaryHike                 1     659235 1266810783 10061
## - YearsInCurrentRole                1     818283 1266969830 10061
## - WorkLifeBalance                   1     835231 1266986779 10061
## - Education                         1     846305 1266997853 10061
## - Age                               1     986857 1267138405 10061
## - `EducationFieldLife Sciences`     1    1409534 1267561082 10061
## - TrainingTimesLastYear             1    1416096 1267567644 10061
## - EducationFieldOther               1    1422861 1267574409 10061
## - EducationFieldMedical             1    1481092 1267632640 10062
## - `EducationFieldTechnical Degree`  1    1821783 1267973331 10062
## - JobInvolvement                    1    3060780 1269212328 10062
## <none>                                           1266151548 10063
## - NumCompaniesWorked                1    3801981 1269953529 10063
## - EnvironmentSatisfaction           1    3810928 1269962476 10063
## - MaritalStatusMarried              1    5132398 1271283946 10064
## - BusinessTravelTravel_Rarely       1    6716220 1272867768 10064
## - EducationFieldMarketing           1    7180821 1273332369 10065
## - YearsWithCurrManager              1   12194979 1278346527 10067
## - DistanceFromHome                  1   12691284 1278842831 10068
## - TotalWorkingYears                 1   49258874 1315410422 10087
## - JobLevel                          1 4380292508 5646444056 11100
## 
## Step:  AIC=10060.99
## .outcome ~ Age + BusinessTravelTravel_Rarely + DistanceFromHome + 
##     Education + `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + JobInvolvement + JobLevel + MaritalStatusMarried + 
##     NumCompaniesWorked + PercentSalaryHike + TotalWorkingYears + 
##     TrainingTimesLastYear + WorkLifeBalance + YearsInCurrentRole + 
##     YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - PercentSalaryHike                 1     676528 1267426305 10059
## - Education                         1     797863 1267547640 10059
## - WorkLifeBalance                   1     807198 1267556975 10059
## - YearsInCurrentRole                1     849710 1267599486 10060
## - Age                               1    1050554 1267800331 10060
## - `EducationFieldLife Sciences`     1    1499162 1268248939 10060
## - TrainingTimesLastYear             1    1510298 1268260075 10060
## - EducationFieldOther               1    1524799 1268274576 10060
## - EducationFieldMedical             1    1564552 1268314329 10060
## - `EducationFieldTechnical Degree`  1    1899334 1268649111 10060
## - JobInvolvement                    1    3308420 1270058197 10061
## - NumCompaniesWorked                1    3550401 1270300178 10061
## <none>                                           1266749777 10061
## - EnvironmentSatisfaction           1    3765835 1270515611 10061
## - MaritalStatusMarried              1    5175611 1271925388 10062
## - BusinessTravelTravel_Rarely       1    6941468 1273691244 10063
## - EducationFieldMarketing           1    7336427 1274086203 10063
## - YearsWithCurrManager              1   12147385 1278897162 10066
## - DistanceFromHome                  1   12598703 1279348480 10066
## - TotalWorkingYears                 1   49430664 1316180441 10086
## - JobLevel                          1 4385292083 5652041860 11098
## 
## Step:  AIC=10059.36
## .outcome ~ Age + BusinessTravelTravel_Rarely + DistanceFromHome + 
##     Education + `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + JobInvolvement + JobLevel + MaritalStatusMarried + 
##     NumCompaniesWorked + TotalWorkingYears + TrainingTimesLastYear + 
##     WorkLifeBalance + YearsInCurrentRole + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - WorkLifeBalance                   1     765304 1268191609 10058
## - Education                         1     797338 1268223643 10058
## - YearsInCurrentRole                1     819806 1268246111 10058
## - Age                               1    1030673 1268456978 10058
## - `EducationFieldLife Sciences`     1    1400604 1268826909 10058
## - EducationFieldOther               1    1429446 1268855751 10058
## - EducationFieldMedical             1    1455102 1268881407 10058
## - TrainingTimesLastYear             1    1529910 1268956215 10058
## - `EducationFieldTechnical Degree`  1    1874685 1269300990 10058
## - JobInvolvement                    1    3295889 1270722195 10059
## - NumCompaniesWorked                1    3615487 1271041792 10059
## <none>                                           1267426305 10059
## - EnvironmentSatisfaction           1    3787407 1271213712 10059
## - MaritalStatusMarried              1    5076278 1272502583 10060
## - BusinessTravelTravel_Rarely       1    7052671 1274478976 10061
## - EducationFieldMarketing           1    7163107 1274589412 10061
## - DistanceFromHome                  1   12305254 1279731559 10064
## - YearsWithCurrManager              1   12352603 1279778908 10064
## - TotalWorkingYears                 1   49379557 1316805862 10084
## - JobLevel                          1 4385002818 5652429123 11096
## 
## Step:  AIC=10057.78
## .outcome ~ Age + BusinessTravelTravel_Rarely + DistanceFromHome + 
##     Education + `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + JobInvolvement + JobLevel + MaritalStatusMarried + 
##     NumCompaniesWorked + TotalWorkingYears + TrainingTimesLastYear + 
##     YearsInCurrentRole + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - Education                         1     786821 1268978430 10056
## - Age                               1     975035 1269166644 10056
## - YearsInCurrentRole                1    1024120 1269215730 10056
## - `EducationFieldLife Sciences`     1    1255053 1269446662 10056
## - EducationFieldMedical             1    1313570 1269505179 10056
## - EducationFieldOther               1    1319367 1269510976 10056
## - TrainingTimesLastYear             1    1480280 1269671890 10057
## - `EducationFieldTechnical Degree`  1    1766492 1269958102 10057
## - JobInvolvement                    1    3303186 1271494795 10058
## <none>                                           1268191609 10058
## - NumCompaniesWorked                1    3846219 1272037829 10058
## - EnvironmentSatisfaction           1    4209176 1272400785 10058
## - MaritalStatusMarried              1    5128610 1273320220 10059
## - EducationFieldMarketing           1    6896534 1275088143 10060
## - BusinessTravelTravel_Rarely       1    7287523 1275479132 10060
## - YearsWithCurrManager              1   12080157 1280271766 10062
## - DistanceFromHome                  1   12262157 1280453767 10062
## - TotalWorkingYears                 1   49600225 1317791834 10082
## - JobLevel                          1 4384268168 5652459777 11094
## 
## Step:  AIC=10056.21
## .outcome ~ Age + BusinessTravelTravel_Rarely + DistanceFromHome + 
##     `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + JobInvolvement + JobLevel + MaritalStatusMarried + 
##     NumCompaniesWorked + TotalWorkingYears + TrainingTimesLastYear + 
##     YearsInCurrentRole + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - Age                               1     731421 1269709851 10055
## - YearsInCurrentRole                1    1069229 1270047659 10055
## - `EducationFieldLife Sciences`     1    1328226 1270306657 10055
## - EducationFieldOther               1    1369289 1270347719 10055
## - EducationFieldMedical             1    1409302 1270387732 10055
## - TrainingTimesLastYear             1    1490327 1270468757 10055
## - `EducationFieldTechnical Degree`  1    1842873 1270821303 10055
## - JobInvolvement                    1    3347447 1272325877 10056
## - NumCompaniesWorked                1    3505126 1272483556 10056
## <none>                                           1268978430 10056
## - EnvironmentSatisfaction           1    4283553 1273261983 10057
## - MaritalStatusMarried              1    5183611 1274162041 10057
## - EducationFieldMarketing           1    6916408 1275894839 10058
## - BusinessTravelTravel_Rarely       1    7490424 1276468854 10058
## - YearsWithCurrManager              1   11615453 1280593883 10060
## - DistanceFromHome                  1   12050622 1281029052 10061
## - TotalWorkingYears                 1   48999860 1317978290 10080
## - JobLevel                          1 4396748789 5665727219 11094
## 
## Step:  AIC=10054.61
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     JobInvolvement + JobLevel + MaritalStatusMarried + NumCompaniesWorked + 
##     TotalWorkingYears + TrainingTimesLastYear + YearsInCurrentRole + 
##     YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - YearsInCurrentRole                1     904094 1270613945 10053
## - EducationFieldOther               1    1363629 1271073480 10053
## - `EducationFieldLife Sciences`     1    1399576 1271109427 10053
## - EducationFieldMedical             1    1464503 1271174354 10053
## - TrainingTimesLastYear             1    1523522 1271233373 10053
## - `EducationFieldTechnical Degree`  1    1916060 1271625911 10054
## - JobInvolvement                    1    3242891 1272952742 10054
## <none>                                           1269709851 10055
## - NumCompaniesWorked                1    3864790 1273574641 10055
## - EnvironmentSatisfaction           1    4521847 1274231698 10055
## - MaritalStatusMarried              1    5012748 1274722599 10055
## - EducationFieldMarketing           1    7195940 1276905791 10056
## - BusinessTravelTravel_Rarely       1    7632892 1277342743 10057
## - YearsWithCurrManager              1   11251518 1280961369 10059
## - DistanceFromHome                  1   11988563 1281698414 10059
## - TotalWorkingYears                 1   58236460 1327946311 10084
## - JobLevel                          1 4405250599 5674960449 11093
## 
## Step:  AIC=10053.1
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     JobInvolvement + JobLevel + MaritalStatusMarried + NumCompaniesWorked + 
##     TotalWorkingYears + TrainingTimesLastYear + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - EducationFieldOther               1    1412588 1272026533 10052
## - `EducationFieldLife Sciences`     1    1472856 1272086801 10052
## - EducationFieldMedical             1    1537071 1272151016 10052
## - TrainingTimesLastYear             1    1546958 1272160903 10052
## - `EducationFieldTechnical Degree`  1    2033218 1272647164 10052
## - JobInvolvement                    1    3260385 1273874330 10053
## - NumCompaniesWorked                1    3422079 1274036024 10053
## <none>                                           1270613945 10053
## - MaritalStatusMarried              1    4710823 1275324768 10054
## - EnvironmentSatisfaction           1    4887883 1275501828 10054
## - EducationFieldMarketing           1    7404083 1278018028 10055
## - BusinessTravelTravel_Rarely       1    7549783 1278163728 10055
## - DistanceFromHome                  1   11923170 1282537115 10058
## - YearsWithCurrManager              1   22619847 1293233792 10063
## - TotalWorkingYears                 1   57735507 1328349452 10082
## - JobLevel                          1 4405034486 5675648432 11091
## 
## Step:  AIC=10051.88
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + JobInvolvement + JobLevel + MaritalStatusMarried + 
##     NumCompaniesWorked + TotalWorkingYears + TrainingTimesLastYear + 
##     YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - `EducationFieldLife Sciences`     1     125168 1272151701 10050
## - EducationFieldMedical             1     171721 1272198254 10050
## - `EducationFieldTechnical Degree`  1     620949 1272647482 10050
## - TrainingTimesLastYear             1    1597141 1273623674 10051
## - JobInvolvement                    1    3269273 1275295806 10052
## - NumCompaniesWorked                1    3471598 1275498131 10052
## <none>                                           1272026533 10052
## - EnvironmentSatisfaction           1    4836080 1276862613 10052
## - MaritalStatusMarried              1    5216076 1277242609 10053
## - BusinessTravelTravel_Rarely       1    7474579 1279501113 10054
## - EducationFieldMarketing           1   10004466 1282030999 10055
## - DistanceFromHome                  1   12012641 1284039174 10056
## - YearsWithCurrManager              1   23015375 1295041908 10062
## - TotalWorkingYears                 1   57760331 1329786865 10081
## - JobLevel                          1 4407851608 5679878141 11090
## 
## Step:  AIC=10049.94
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + EducationFieldMarketing + 
##     EducationFieldMedical + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + JobInvolvement + JobLevel + MaritalStatusMarried + 
##     NumCompaniesWorked + TotalWorkingYears + TrainingTimesLastYear + 
##     YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - EducationFieldMedical             1      47387 1272199088 10048
## - `EducationFieldTechnical Degree`  1     546389 1272698091 10048
## - TrainingTimesLastYear             1    1632410 1273784112 10049
## - JobInvolvement                    1    3219635 1275371337 10050
## - NumCompaniesWorked                1    3540478 1275692179 10050
## <none>                                           1272151701 10050
## - EnvironmentSatisfaction           1    4753271 1276904973 10050
## - MaritalStatusMarried              1    5284603 1277436304 10051
## - BusinessTravelTravel_Rarely       1    7501175 1279652876 10052
## - DistanceFromHome                  1   11945106 1284096807 10054
## - EducationFieldMarketing           1   16351509 1288503210 10057
## - YearsWithCurrManager              1   23416220 1295567921 10061
## - TotalWorkingYears                 1   57677595 1329829297 10079
## - JobLevel                          1 4419107639 5691259340 11089
## 
## Step:  AIC=10047.97
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + EducationFieldMarketing + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     JobInvolvement + JobLevel + MaritalStatusMarried + NumCompaniesWorked + 
##     TotalWorkingYears + TrainingTimesLastYear + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - `EducationFieldTechnical Degree`  1     500763 1272699851 10046
## - TrainingTimesLastYear             1    1590650 1273789739 10047
## - JobInvolvement                    1    3217325 1275416414 10048
## - NumCompaniesWorked                1    3542204 1275741292 10048
## <none>                                           1272199088 10048
## - EnvironmentSatisfaction           1    4738118 1276937206 10049
## - MaritalStatusMarried              1    5282840 1277481928 10049
## - BusinessTravelTravel_Rarely       1    7531826 1279730914 10050
## - DistanceFromHome                  1   12148042 1284347130 10053
## - EducationFieldMarketing           1   17050728 1289249816 10055
## - YearsWithCurrManager              1   23440726 1295639814 10059
## - TotalWorkingYears                 1   57782645 1329981733 10077
## - JobLevel                          1 4436639581 5708838669 11089
## 
## Step:  AIC=10046.24
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + EducationFieldMarketing + 
##     EnvironmentSatisfaction + JobInvolvement + JobLevel + MaritalStatusMarried + 
##     NumCompaniesWorked + TotalWorkingYears + TrainingTimesLastYear + 
##     YearsWithCurrManager
## 
##                               Df  Sum of Sq        RSS   AIC
## - TrainingTimesLastYear        1    1595367 1274295218 10045
## - JobInvolvement               1    3169284 1275869135 10046
## - NumCompaniesWorked           1    3540973 1276240824 10046
## <none>                                      1272699851 10046
## - EnvironmentSatisfaction      1    4974852 1277674703 10047
## - MaritalStatusMarried         1    5141800 1277841651 10047
## - BusinessTravelTravel_Rarely  1    7570796 1280270646 10048
## - DistanceFromHome             1   12176261 1284876112 10051
## - EducationFieldMarketing      1   16618248 1289318099 10053
## - YearsWithCurrManager         1   23544705 1296244556 10057
## - TotalWorkingYears            1   58308051 1331007902 10075
## - JobLevel                     1 4436209422 5708909273 11087
## 
## Step:  AIC=10045.11
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + EducationFieldMarketing + 
##     EnvironmentSatisfaction + JobInvolvement + JobLevel + MaritalStatusMarried + 
##     NumCompaniesWorked + TotalWorkingYears + YearsWithCurrManager
## 
##                               Df  Sum of Sq        RSS   AIC
## - JobInvolvement               1    3138047 1277433264 10045
## <none>                                      1274295218 10045
## - NumCompaniesWorked           1    3808263 1278103481 10045
## - MaritalStatusMarried         1    4782374 1279077592 10046
## - EnvironmentSatisfaction      1    5064504 1279359721 10046
## - BusinessTravelTravel_Rarely  1    7524454 1281819672 10047
## - DistanceFromHome             1   12443468 1286738686 10050
## - EducationFieldMarketing      1   16677617 1290972835 10052
## - YearsWithCurrManager         1   23515983 1297811201 10056
## - TotalWorkingYears            1   58243109 1332538326 10074
## - JobLevel                     1 4435079082 5709374300 11085
## 
## Step:  AIC=10044.82
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + EducationFieldMarketing + 
##     EnvironmentSatisfaction + JobLevel + MaritalStatusMarried + 
##     NumCompaniesWorked + TotalWorkingYears + YearsWithCurrManager
## 
##                               Df  Sum of Sq        RSS   AIC
## <none>                                      1277433264 10045
## - NumCompaniesWorked           1    3937832 1281371096 10045
## - MaritalStatusMarried         1    4843807 1282277071 10046
## - EnvironmentSatisfaction      1    4992323 1282425587 10046
## - BusinessTravelTravel_Rarely  1    7981170 1285414435 10047
## - DistanceFromHome             1   12746752 1290180016 10050
## - EducationFieldMarketing      1   16771270 1294204534 10052
## - YearsWithCurrManager         1   23179559 1300612823 10055
## - TotalWorkingYears            1   58411771 1335845035 10074
## - JobLevel                     1 4437703659 5715136924 11084
## Start:  AIC=10073.86
## .outcome ~ Age + BusinessTravelTravel_Frequently + BusinessTravelTravel_Rarely + 
##     DistanceFromHome + Education + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     GenderMale + JobInvolvement + JobLevel + JobSatisfaction + 
##     MaritalStatusMarried + MaritalStatusSingle + NumCompaniesWorked + 
##     OverTimeYes + PercentSalaryHike + RelationshipSatisfaction + 
##     StockOptionLevel + TotalWorkingYears + TrainingTimesLastYear + 
##     WorkLifeBalance + YearsInCurrentRole + YearsSinceLastPromotion + 
##     YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - BusinessTravelTravel_Frequently   1       1843 1213469564 10072
## - WorkLifeBalance                   1       5321 1213473042 10072
## - NumCompaniesWorked                1      16649 1213484370 10072
## - PercentSalaryHike                 1      27063 1213494784 10072
## - TrainingTimesLastYear             1      47700 1213515421 10072
## - JobSatisfaction                   1      54234 1213521955 10072
## - Education                         1     210574 1213678295 10072
## - YearsSinceLastPromotion           1     217098 1213684819 10072
## - OverTimeYes                       1     299483 1213767204 10072
## - YearsInCurrentRole                1     319874 1213787595 10072
## - MaritalStatusMarried              1     483083 1213950805 10072
## - StockOptionLevel                  1     500498 1213968219 10072
## - Age                               1     566388 1214034109 10072
## - RelationshipSatisfaction          1     835801 1214303523 10072
## - GenderMale                        1    1420748 1214888469 10073
## - EducationFieldMedical             1    1447830 1214915551 10073
## - MaritalStatusSingle               1    1672277 1215139999 10073
## - `EducationFieldLife Sciences`     1    2144611 1215612332 10073
## - `EducationFieldTechnical Degree`  1    2582694 1216050415 10073
## - EnvironmentSatisfaction           1    2623668 1216091389 10073
## <none>                                           1213467721 10074
## - EducationFieldOther               1    4220139 1217687860 10074
## - BusinessTravelTravel_Rarely       1    4488390 1217956111 10074
## - JobInvolvement                    1    5156034 1218623755 10075
## - EducationFieldMarketing           1    6751809 1220219530 10076
## - DistanceFromHome                  1    8216152 1221683873 10077
## - YearsWithCurrManager              1   10246157 1223713878 10078
## - TotalWorkingYears                 1   37533221 1251000942 10093
## - JobLevel                          1 4167298350 5380766071 11110
## 
## Step:  AIC=10071.86
## .outcome ~ Age + BusinessTravelTravel_Rarely + DistanceFromHome + 
##     Education + `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     JobSatisfaction + MaritalStatusMarried + MaritalStatusSingle + 
##     NumCompaniesWorked + OverTimeYes + PercentSalaryHike + RelationshipSatisfaction + 
##     StockOptionLevel + TotalWorkingYears + TrainingTimesLastYear + 
##     WorkLifeBalance + YearsInCurrentRole + YearsSinceLastPromotion + 
##     YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - WorkLifeBalance                   1       5213 1213474777 10070
## - NumCompaniesWorked                1      16310 1213485874 10070
## - PercentSalaryHike                 1      27349 1213496913 10070
## - TrainingTimesLastYear             1      48385 1213517949 10070
## - JobSatisfaction                   1      55334 1213524899 10070
## - Education                         1     212494 1213682058 10070
## - YearsSinceLastPromotion           1     217420 1213686984 10070
## - OverTimeYes                       1     303148 1213772712 10070
## - YearsInCurrentRole                1     319882 1213789446 10070
## - MaritalStatusMarried              1     489107 1213958671 10070
## - StockOptionLevel                  1     498859 1213968423 10070
## - Age                               1     570360 1214039925 10070
## - RelationshipSatisfaction          1     838477 1214308041 10070
## - GenderMale                        1    1418933 1214888498 10071
## - EducationFieldMedical             1    1446844 1214916408 10071
## - MaritalStatusSingle               1    1675714 1215145279 10071
## - `EducationFieldLife Sciences`     1    2142992 1215612557 10071
## - `EducationFieldTechnical Degree`  1    2580852 1216050417 10071
## - EnvironmentSatisfaction           1    2621839 1216091403 10071
## <none>                                           1213469564 10072
## - EducationFieldOther               1    4218435 1217687999 10072
## - JobInvolvement                    1    5217182 1218686746 10073
## - EducationFieldMarketing           1    6751441 1220221006 10074
## - DistanceFromHome                  1    8225665 1221695229 10075
## - BusinessTravelTravel_Rarely       1    9028520 1222498084 10075
## - YearsWithCurrManager              1   10257163 1223726728 10076
## - TotalWorkingYears                 1   37808695 1251278259 10091
## - JobLevel                          1 4171678578 5385148142 11108
## 
## Step:  AIC=10069.87
## .outcome ~ Age + BusinessTravelTravel_Rarely + DistanceFromHome + 
##     Education + `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     JobSatisfaction + MaritalStatusMarried + MaritalStatusSingle + 
##     NumCompaniesWorked + OverTimeYes + PercentSalaryHike + RelationshipSatisfaction + 
##     StockOptionLevel + TotalWorkingYears + TrainingTimesLastYear + 
##     YearsInCurrentRole + YearsSinceLastPromotion + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - NumCompaniesWorked                1      16612 1213491389 10068
## - PercentSalaryHike                 1      26828 1213501605 10068
## - TrainingTimesLastYear             1      49463 1213524241 10068
## - JobSatisfaction                   1      53577 1213528354 10068
## - Education                         1     211602 1213686379 10068
## - YearsSinceLastPromotion           1     218089 1213692866 10068
## - OverTimeYes                       1     301695 1213776473 10068
## - YearsInCurrentRole                1     315374 1213790152 10068
## - MaritalStatusMarried              1     487839 1213962616 10068
## - StockOptionLevel                  1     494719 1213969496 10068
## - Age                               1     570830 1214045607 10068
## - RelationshipSatisfaction          1     842272 1214317049 10068
## - GenderMale                        1    1415077 1214889854 10069
## - EducationFieldMedical             1    1447334 1214922112 10069
## - MaritalStatusSingle               1    1673552 1215148329 10069
## - `EducationFieldLife Sciences`     1    2144959 1215619736 10069
## - `EducationFieldTechnical Degree`  1    2580696 1216055473 10069
## - EnvironmentSatisfaction           1    2616698 1216091475 10069
## <none>                                           1213474777 10070
## - EducationFieldOther               1    4214569 1217689346 10070
## - JobInvolvement                    1    5222810 1218697588 10071
## - EducationFieldMarketing           1    6750092 1220224869 10072
## - DistanceFromHome                  1    8232717 1221707495 10073
## - BusinessTravelTravel_Rarely       1    9033967 1222508745 10073
## - YearsWithCurrManager              1   10289690 1223764468 10074
## - TotalWorkingYears                 1   37822694 1251297472 10089
## - JobLevel                          1 4174789387 5388264165 11107
## 
## Step:  AIC=10067.88
## .outcome ~ Age + BusinessTravelTravel_Rarely + DistanceFromHome + 
##     Education + `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     JobSatisfaction + MaritalStatusMarried + MaritalStatusSingle + 
##     OverTimeYes + PercentSalaryHike + RelationshipSatisfaction + 
##     StockOptionLevel + TotalWorkingYears + TrainingTimesLastYear + 
##     YearsInCurrentRole + YearsSinceLastPromotion + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - PercentSalaryHike                 1      27945 1213519334 10066
## - TrainingTimesLastYear             1      45068 1213536457 10066
## - JobSatisfaction                   1      50778 1213542167 10066
## - Education                         1     199752 1213691141 10066
## - YearsSinceLastPromotion           1     206190 1213697580 10066
## - OverTimeYes                       1     302971 1213794360 10066
## - YearsInCurrentRole                1     335744 1213827133 10066
## - StockOptionLevel                  1     487331 1213978720 10066
## - MaritalStatusMarried              1     490834 1213982223 10066
## - Age                               1     556570 1214047959 10066
## - RelationshipSatisfaction          1     867684 1214359074 10066
## - GenderMale                        1    1413771 1214905160 10067
## - EducationFieldMedical             1    1434375 1214925764 10067
## - MaritalStatusSingle               1    1663945 1215155335 10067
## - `EducationFieldLife Sciences`     1    2129775 1215621164 10067
## - `EducationFieldTechnical Degree`  1    2565018 1216056407 10067
## - EnvironmentSatisfaction           1    2607198 1216098588 10067
## <none>                                           1213491389 10068
## - EducationFieldOther               1    4197985 1217689374 10068
## - JobInvolvement                    1    5216153 1218707542 10069
## - EducationFieldMarketing           1    6733597 1220224986 10070
## - DistanceFromHome                  1    8334658 1221826048 10071
## - BusinessTravelTravel_Rarely       1    9023724 1222515113 10071
## - YearsWithCurrManager              1   10466561 1223957951 10072
## - TotalWorkingYears                 1   40507052 1253998441 10089
## - JobLevel                          1 4196380101 5409871491 11108
## 
## Step:  AIC=10065.89
## .outcome ~ Age + BusinessTravelTravel_Rarely + DistanceFromHome + 
##     Education + `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     JobSatisfaction + MaritalStatusMarried + MaritalStatusSingle + 
##     OverTimeYes + RelationshipSatisfaction + StockOptionLevel + 
##     TotalWorkingYears + TrainingTimesLastYear + YearsInCurrentRole + 
##     YearsSinceLastPromotion + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - TrainingTimesLastYear             1      45606 1213564940 10064
## - JobSatisfaction                   1      47071 1213566405 10064
## - Education                         1     204358 1213723692 10064
## - YearsSinceLastPromotion           1     215290 1213734624 10064
## - OverTimeYes                       1     303521 1213822856 10064
## - YearsInCurrentRole                1     344981 1213864315 10064
## - StockOptionLevel                  1     484406 1214003740 10064
## - MaritalStatusMarried              1     492103 1214011437 10064
## - Age                               1     555123 1214074457 10064
## - RelationshipSatisfaction          1     886648 1214405982 10064
## - GenderMale                        1    1421699 1214941033 10065
## - EducationFieldMedical             1    1424146 1214943480 10065
## - MaritalStatusSingle               1    1665816 1215185150 10065
## - `EducationFieldLife Sciences`     1    2112863 1215632197 10065
## - `EducationFieldTechnical Degree`  1    2541681 1216061015 10065
## - EnvironmentSatisfaction           1    2627884 1216147218 10065
## <none>                                           1213519334 10066
## - EducationFieldOther               1    4178502 1217697836 10066
## - JobInvolvement                    1    5199033 1218718367 10067
## - EducationFieldMarketing           1    6705653 1220224987 10068
## - DistanceFromHome                  1    8388684 1221908018 10069
## - BusinessTravelTravel_Rarely       1    9094741 1222614075 10069
## - YearsWithCurrManager              1   10447228 1223966562 10070
## - TotalWorkingYears                 1   40739815 1254259149 10087
## - JobLevel                          1 4197940223 5411459557 11106
## 
## Step:  AIC=10063.92
## .outcome ~ Age + BusinessTravelTravel_Rarely + DistanceFromHome + 
##     Education + `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     JobSatisfaction + MaritalStatusMarried + MaritalStatusSingle + 
##     OverTimeYes + RelationshipSatisfaction + StockOptionLevel + 
##     TotalWorkingYears + YearsInCurrentRole + YearsSinceLastPromotion + 
##     YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - JobSatisfaction                   1      44828 1213609768 10062
## - YearsSinceLastPromotion           1     209619 1213774559 10062
## - Education                         1     215755 1213780695 10062
## - OverTimeYes                       1     290856 1213855796 10062
## - YearsInCurrentRole                1     344607 1213909547 10062
## - StockOptionLevel                  1     475462 1214040402 10062
## - MaritalStatusMarried              1     487546 1214052486 10062
## - Age                               1     566167 1214131107 10062
## - RelationshipSatisfaction          1     888641 1214453581 10062
## - EducationFieldMedical             1    1404542 1214969482 10063
## - GenderMale                        1    1426080 1214991020 10063
## - MaritalStatusSingle               1    1670308 1215235248 10063
## - `EducationFieldLife Sciences`     1    2100601 1215665541 10063
## - `EducationFieldTechnical Degree`  1    2523875 1216088814 10063
## - EnvironmentSatisfaction           1    2628374 1216193313 10063
## <none>                                           1213564940 10064
## - EducationFieldOther               1    4155927 1217720867 10064
## - JobInvolvement                    1    5191842 1218756782 10065
## - EducationFieldMarketing           1    6683124 1220248064 10066
## - DistanceFromHome                  1    8472874 1222037814 10067
## - BusinessTravelTravel_Rarely       1    9074235 1222639175 10067
## - YearsWithCurrManager              1   10417745 1223982685 10068
## - TotalWorkingYears                 1   40734451 1254299391 10085
## - JobLevel                          1 4200229675 5413794615 11104
## 
## Step:  AIC=10061.95
## .outcome ~ Age + BusinessTravelTravel_Rarely + DistanceFromHome + 
##     Education + `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     MaritalStatusMarried + MaritalStatusSingle + OverTimeYes + 
##     RelationshipSatisfaction + StockOptionLevel + TotalWorkingYears + 
##     YearsInCurrentRole + YearsSinceLastPromotion + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - YearsSinceLastPromotion           1     210699 1213820467 10060
## - Education                         1     216099 1213825867 10060
## - OverTimeYes                       1     297670 1213907438 10060
## - YearsInCurrentRole                1     338880 1213948648 10060
## - StockOptionLevel                  1     469229 1214078997 10060
## - MaritalStatusMarried              1     487955 1214097723 10060
## - Age                               1     554670 1214164438 10060
## - RelationshipSatisfaction          1     870285 1214480053 10060
## - EducationFieldMedical             1    1398041 1215007808 10061
## - GenderMale                        1    1427213 1215036981 10061
## - MaritalStatusSingle               1    1650011 1215259779 10061
## - `EducationFieldLife Sciences`     1    2091123 1215700891 10061
## - `EducationFieldTechnical Degree`  1    2525211 1216134979 10061
## - EnvironmentSatisfaction           1    2647156 1216256924 10062
## <none>                                           1213609768 10062
## - EducationFieldOther               1    4139288 1217749055 10062
## - JobInvolvement                    1    5168318 1218778086 10063
## - EducationFieldMarketing           1    6665040 1220274808 10064
## - DistanceFromHome                  1    8493150 1222102918 10065
## - BusinessTravelTravel_Rarely       1    9036358 1222646126 10065
## - YearsWithCurrManager              1   10409448 1224019216 10066
## - TotalWorkingYears                 1   40695197 1254304965 10083
## - JobLevel                          1 4203900866 5417510634 11103
## 
## Step:  AIC=10060.07
## .outcome ~ Age + BusinessTravelTravel_Rarely + DistanceFromHome + 
##     Education + `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     MaritalStatusMarried + MaritalStatusSingle + OverTimeYes + 
##     RelationshipSatisfaction + StockOptionLevel + TotalWorkingYears + 
##     YearsInCurrentRole + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - Education                         1     204656 1214025123 10058
## - YearsInCurrentRole                1     247225 1214067692 10058
## - OverTimeYes                       1     306390 1214126857 10058
## - MaritalStatusMarried              1     493453 1214313921 10058
## - StockOptionLevel                  1     501520 1214321988 10058
## - Age                               1     602180 1214422648 10058
## - RelationshipSatisfaction          1     899077 1214719544 10059
## - EducationFieldMedical             1    1338278 1215158746 10059
## - GenderMale                        1    1477625 1215298092 10059
## - MaritalStatusSingle               1    1705627 1215526094 10059
## - `EducationFieldLife Sciences`     1    2024158 1215844625 10059
## - `EducationFieldTechnical Degree`  1    2437225 1216257692 10060
## - EnvironmentSatisfaction           1    2618626 1216439093 10060
## <none>                                           1213820467 10060
## - EducationFieldOther               1    4080299 1217900766 10060
## - JobInvolvement                    1    5105020 1218925487 10061
## - EducationFieldMarketing           1    6571837 1220392304 10062
## - DistanceFromHome                  1    8524498 1222344965 10063
## - BusinessTravelTravel_Rarely       1    8860052 1222680519 10063
## - YearsWithCurrManager              1   10205038 1224025505 10064
## - TotalWorkingYears                 1   43191737 1257012204 10082
## - JobLevel                          1 4203731578 5417552045 11101
## 
## Step:  AIC=10058.18
## .outcome ~ Age + BusinessTravelTravel_Rarely + DistanceFromHome + 
##     `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     MaritalStatusMarried + MaritalStatusSingle + OverTimeYes + 
##     RelationshipSatisfaction + StockOptionLevel + TotalWorkingYears + 
##     YearsInCurrentRole + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - YearsInCurrentRole                1     243993 1214269117 10056
## - OverTimeYes                       1     318160 1214343284 10056
## - MaritalStatusMarried              1     489217 1214514341 10056
## - StockOptionLevel                  1     515232 1214540355 10056
## - Age                               1     719699 1214744823 10057
## - RelationshipSatisfaction          1     903000 1214928123 10057
## - EducationFieldMedical             1    1315029 1215340152 10057
## - GenderMale                        1    1514559 1215539683 10057
## - MaritalStatusSingle               1    1720676 1215745799 10057
## - `EducationFieldLife Sciences`     1    1994291 1216019414 10057
## - `EducationFieldTechnical Degree`  1    2430629 1216455753 10058
## - EnvironmentSatisfaction           1    2559824 1216584947 10058
## <none>                                           1214025123 10058
## - EducationFieldOther               1    4053663 1218078786 10058
## - JobInvolvement                    1    5044807 1219069930 10059
## - EducationFieldMarketing           1    6624026 1220649149 10060
## - DistanceFromHome                  1    8668663 1222693786 10061
## - BusinessTravelTravel_Rarely       1    8808025 1222833148 10061
## - YearsWithCurrManager              1   10304820 1224329943 10062
## - TotalWorkingYears                 1   43173473 1257198596 10080
## - JobLevel                          1 4203706166 5417731289 11099
## 
## Step:  AIC=10056.32
## .outcome ~ Age + BusinessTravelTravel_Rarely + DistanceFromHome + 
##     `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     MaritalStatusMarried + MaritalStatusSingle + OverTimeYes + 
##     RelationshipSatisfaction + StockOptionLevel + TotalWorkingYears + 
##     YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - OverTimeYes                       1     307098 1214576215 10054
## - MaritalStatusMarried              1     456596 1214725713 10055
## - StockOptionLevel                  1     544858 1214813974 10055
## - Age                               1     678583 1214947699 10055
## - RelationshipSatisfaction          1     898628 1215167745 10055
## - EducationFieldMedical             1    1317236 1215586353 10055
## - GenderMale                        1    1559685 1215828802 10055
## - MaritalStatusSingle               1    1721321 1215990438 10055
## - `EducationFieldLife Sciences`     1    1997327 1216266444 10056
## - `EducationFieldTechnical Degree`  1    2466214 1216735331 10056
## - EnvironmentSatisfaction           1    2637593 1216906710 10056
## <none>                                           1214269117 10056
## - EducationFieldOther               1    4071293 1218340410 10057
## - JobInvolvement                    1    4982832 1219251948 10057
## - EducationFieldMarketing           1    6616084 1220885200 10058
## - BusinessTravelTravel_Rarely       1    8677189 1222946306 10059
## - DistanceFromHome                  1    8721626 1222990743 10059
## - YearsWithCurrManager              1   20509171 1234778287 10066
## - TotalWorkingYears                 1   43209264 1257478381 10079
## - JobLevel                          1 4208226358 5422495475 11097
## 
## Step:  AIC=10054.5
## .outcome ~ Age + BusinessTravelTravel_Rarely + DistanceFromHome + 
##     `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     MaritalStatusMarried + MaritalStatusSingle + RelationshipSatisfaction + 
##     StockOptionLevel + TotalWorkingYears + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - MaritalStatusMarried              1     458739 1215034954 10053
## - StockOptionLevel                  1     527189 1215103403 10053
## - Age                               1     662070 1215238285 10053
## - RelationshipSatisfaction          1     899055 1215475270 10053
## - EducationFieldMedical             1    1261244 1215837459 10053
## - GenderMale                        1    1553621 1216129835 10053
## - MaritalStatusSingle               1    1697590 1216273804 10054
## - `EducationFieldLife Sciences`     1    1935841 1216512055 10054
## - `EducationFieldTechnical Degree`  1    2429006 1217005221 10054
## - EnvironmentSatisfaction           1    2511092 1217087307 10054
## <none>                                           1214576215 10054
## - EducationFieldOther               1    3972094 1218548309 10055
## - JobInvolvement                    1    4921177 1219497391 10055
## - EducationFieldMarketing           1    6521298 1221097513 10056
## - DistanceFromHome                  1    8496108 1223072323 10057
## - BusinessTravelTravel_Rarely       1    8790837 1223367051 10058
## - YearsWithCurrManager              1   20781636 1235357851 10064
## - TotalWorkingYears                 1   43125717 1257701931 10077
## - JobLevel                          1 4211913177 5426489391 11096
## 
## Step:  AIC=10052.76
## .outcome ~ Age + BusinessTravelTravel_Rarely + DistanceFromHome + 
##     `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     MaritalStatusSingle + RelationshipSatisfaction + StockOptionLevel + 
##     TotalWorkingYears + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - Age                               1     671696 1215706649 10051
## - RelationshipSatisfaction          1     874760 1215909713 10051
## - StockOptionLevel                  1     954406 1215989359 10051
## - EducationFieldMedical             1    1351507 1216386460 10052
## - GenderMale                        1    1431918 1216466872 10052
## - `EducationFieldLife Sciences`     1    2033297 1217068251 10052
## - `EducationFieldTechnical Degree`  1    2516226 1217551179 10052
## - EnvironmentSatisfaction           1    2567748 1217602702 10052
## <none>                                           1215034954 10053
## - EducationFieldOther               1    4128344 1219163298 10053
## - JobInvolvement                    1    4781835 1219816789 10054
## - MaritalStatusSingle               1    5205526 1220240480 10054
## - EducationFieldMarketing           1    6769732 1221804686 10055
## - DistanceFromHome                  1    8225296 1223260250 10056
## - BusinessTravelTravel_Rarely       1    9130920 1224165874 10056
## - YearsWithCurrManager              1   20621962 1235656915 10062
## - TotalWorkingYears                 1   43357294 1258392248 10075
## - JobLevel                          1 4213522411 5428557365 11094
## 
## Step:  AIC=10051.15
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     GenderMale + JobInvolvement + JobLevel + MaritalStatusSingle + 
##     RelationshipSatisfaction + StockOptionLevel + TotalWorkingYears + 
##     YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - RelationshipSatisfaction          1     793729 1216500379 10050
## - StockOptionLevel                  1     889959 1216596609 10050
## - EducationFieldMedical             1    1323663 1217030313 10050
## - GenderMale                        1    1442205 1217148854 10050
## - `EducationFieldLife Sciences`     1    2046169 1217752818 10050
## - `EducationFieldTechnical Degree`  1    2477609 1218184259 10051
## - EnvironmentSatisfaction           1    2582731 1218289380 10051
## <none>                                           1215706649 10051
## - EducationFieldOther               1    4014201 1219720850 10051
## - JobInvolvement                    1    4667816 1220374466 10052
## - MaritalStatusSingle               1    4846640 1220553289 10052
## - EducationFieldMarketing           1    6820983 1222527632 10053
## - DistanceFromHome                  1    8258273 1223964922 10054
## - BusinessTravelTravel_Rarely       1    9218382 1224925031 10054
## - YearsWithCurrManager              1   19956049 1235662698 10060
## - TotalWorkingYears                 1   52706140 1268412789 10079
## - JobLevel                          1 4242191482 5457898132 11096
## 
## Step:  AIC=10049.6
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     GenderMale + JobInvolvement + JobLevel + MaritalStatusSingle + 
##     StockOptionLevel + TotalWorkingYears + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - StockOptionLevel                  1     894099 1217394478 10048
## - EducationFieldMedical             1    1351145 1217851524 10048
## - GenderMale                        1    1438695 1217939074 10048
## - `EducationFieldLife Sciences`     1    2148655 1218649034 10049
## - EnvironmentSatisfaction           1    2474590 1218974968 10049
## - `EducationFieldTechnical Degree`  1    2550030 1219050409 10049
## <none>                                           1216500379 10050
## - EducationFieldOther               1    4112624 1220613002 10050
## - MaritalStatusSingle               1    4624203 1221124582 10050
## - JobInvolvement                    1    4770831 1221271209 10050
## - EducationFieldMarketing           1    7004401 1223504780 10052
## - DistanceFromHome                  1    8094644 1224595023 10052
## - BusinessTravelTravel_Rarely       1    8999186 1225499565 10053
## - YearsWithCurrManager              1   20128971 1236629350 10059
## - TotalWorkingYears                 1   52542068 1269042447 10077
## - JobLevel                          1 4247986899 5464487278 11095
## 
## Step:  AIC=10048.12
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     GenderMale + JobInvolvement + JobLevel + MaritalStatusSingle + 
##     TotalWorkingYears + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - GenderMale                        1    1389894 1218784372 10047
## - EducationFieldMedical             1    1410260 1218804738 10047
## - `EducationFieldLife Sciences`     1    2222282 1219616760 10047
## - `EducationFieldTechnical Degree`  1    2581181 1219975658 10048
## - EnvironmentSatisfaction           1    2679919 1220074396 10048
## <none>                                           1217394478 10048
## - MaritalStatusSingle               1    3952144 1221346622 10048
## - EducationFieldOther               1    4057845 1221452323 10048
## - JobInvolvement                    1    4554095 1221948573 10049
## - EducationFieldMarketing           1    7188049 1224582527 10050
## - DistanceFromHome                  1    8355675 1225750153 10051
## - BusinessTravelTravel_Rarely       1    9201495 1226595973 10051
## - YearsWithCurrManager              1   20417630 1237812108 10058
## - TotalWorkingYears                 1   52360652 1269755130 10076
## - JobLevel                          1 4263345887 5480740365 11095
## 
## Step:  AIC=10046.91
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     JobInvolvement + JobLevel + MaritalStatusSingle + TotalWorkingYears + 
##     YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - EducationFieldMedical             1    1459112 1220243484 10046
## - `EducationFieldLife Sciences`     1    2270240 1221054612 10046
## - `EducationFieldTechnical Degree`  1    2644941 1221429313 10046
## - EnvironmentSatisfaction           1    2675744 1221460116 10046
## <none>                                           1218784372 10047
## - MaritalStatusSingle               1    3936838 1222721209 10047
## - EducationFieldOther               1    4093574 1222877946 10047
## - JobInvolvement                    1    4716749 1223501121 10048
## - EducationFieldMarketing           1    7273205 1226057576 10049
## - DistanceFromHome                  1    8473264 1227257636 10050
## - BusinessTravelTravel_Rarely       1    9222339 1228006711 10050
## - YearsWithCurrManager              1   21327148 1240111520 10057
## - TotalWorkingYears                 1   51831221 1270615593 10074
## - JobLevel                          1 4263701648 5482486020 11093
## 
## Step:  AIC=10045.74
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + JobInvolvement + JobLevel + MaritalStatusSingle + 
##     TotalWorkingYears + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - `EducationFieldLife Sciences`     1    1676492 1221919975 10045
## - `EducationFieldTechnical Degree`  1    1676911 1221920395 10045
## - EnvironmentSatisfaction           1    2738049 1222981533 10045
## <none>                                           1220243484 10046
## - MaritalStatusSingle               1    4136796 1224380279 10046
## - EducationFieldOther               1    4258765 1224502249 10046
## - JobInvolvement                    1    4969140 1225212623 10047
## - DistanceFromHome                  1    8502055 1228745539 10049
## - BusinessTravelTravel_Rarely       1    8954213 1229197697 10049
## - EducationFieldMarketing           1   18431267 1238674750 10054
## - YearsWithCurrManager              1   21978562 1242222046 10056
## - TotalWorkingYears                 1   52281215 1272524699 10073
## - JobLevel                          1 4266366428 5486609912 11092
## 
## Step:  AIC=10044.7
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + EducationFieldMarketing + 
##     EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + JobInvolvement + JobLevel + MaritalStatusSingle + 
##     TotalWorkingYears + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - `EducationFieldTechnical Degree`  1     801761 1222721736 10043
## - EnvironmentSatisfaction           1    2695932 1224615907 10044
## - EducationFieldOther               1    3093785 1225013760 10044
## <none>                                           1221919975 10045
## - MaritalStatusSingle               1    4089745 1226009721 10045
## - JobInvolvement                    1    4836320 1226756295 10046
## - DistanceFromHome                  1    8279712 1230199687 10047
## - BusinessTravelTravel_Rarely       1    8773827 1230693802 10048
## - EducationFieldMarketing           1   16926926 1238846901 10052
## - YearsWithCurrManager              1   21716898 1243636873 10055
## - TotalWorkingYears                 1   53529363 1275449338 10073
## - JobLevel                          1 4265260175 5487180150 11090
## 
## Step:  AIC=10043.16
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + EducationFieldMarketing + 
##     EducationFieldOther + EnvironmentSatisfaction + JobInvolvement + 
##     JobLevel + MaritalStatusSingle + TotalWorkingYears + YearsWithCurrManager
## 
##                               Df  Sum of Sq        RSS   AIC
## - EnvironmentSatisfaction      1    2746147 1225467883 10043
## - EducationFieldOther          1    2833764 1225555500 10043
## <none>                                      1222721736 10043
## - MaritalStatusSingle          1    4059739 1226781475 10044
## - JobInvolvement               1    4892529 1227614265 10044
## - DistanceFromHome             1    8211604 1230933340 10046
## - BusinessTravelTravel_Rarely  1    8723886 1231445622 10046
## - EducationFieldMarketing      1   16280002 1239001738 10050
## - YearsWithCurrManager         1   21672383 1244394119 10053
## - TotalWorkingYears            1   53911777 1276633513 10071
## - JobLevel                     1 4264663842 5487385578 11088
## 
## Step:  AIC=10042.72
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + EducationFieldMarketing + 
##     EducationFieldOther + JobInvolvement + JobLevel + MaritalStatusSingle + 
##     TotalWorkingYears + YearsWithCurrManager
## 
##                               Df  Sum of Sq        RSS   AIC
## - EducationFieldOther          1    3314802 1228782685 10043
## <none>                                      1225467883 10043
## - MaritalStatusSingle          1    4042358 1229510241 10043
## - JobInvolvement               1    4997988 1230465871 10044
## - DistanceFromHome             1    7992939 1233460822 10045
## - BusinessTravelTravel_Rarely  1    8770639 1234238521 10046
## - EducationFieldMarketing      1   16226727 1241694609 10050
## - YearsWithCurrManager         1   21230533 1246698415 10053
## - TotalWorkingYears            1   54733496 1280201379 10071
## - JobLevel                     1 4262496454 5487964336 11086
## 
## Step:  AIC=10042.61
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + EducationFieldMarketing + 
##     JobInvolvement + JobLevel + MaritalStatusSingle + TotalWorkingYears + 
##     YearsWithCurrManager
## 
##                               Df  Sum of Sq        RSS   AIC
## <none>                                      1228782685 10043
## - MaritalStatusSingle          1    4118761 1232901445 10043
## - JobInvolvement               1    5208967 1233991651 10044
## - DistanceFromHome             1    8106648 1236889333 10045
## - BusinessTravelTravel_Rarely  1    8429117 1237211802 10045
## - EducationFieldMarketing      1   14981210 1243763895 10049
## - YearsWithCurrManager         1   21292513 1250075198 10053
## - TotalWorkingYears            1   55471856 1284254540 10071
## - JobLevel                     1 4259894926 5488677610 11084
## Start:  AIC=10093.6
## .outcome ~ Age + BusinessTravelTravel_Frequently + BusinessTravelTravel_Rarely + 
##     DistanceFromHome + Education + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     GenderMale + JobInvolvement + JobLevel + JobSatisfaction + 
##     MaritalStatusMarried + MaritalStatusSingle + NumCompaniesWorked + 
##     OverTimeYes + PercentSalaryHike + RelationshipSatisfaction + 
##     StockOptionLevel + TotalWorkingYears + TrainingTimesLastYear + 
##     WorkLifeBalance + YearsInCurrentRole + YearsSinceLastPromotion + 
##     YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - MaritalStatusSingle               1        803 1272580452 10092
## - YearsInCurrentRole                1      40660 1272620309 10092
## - BusinessTravelTravel_Frequently   1      45759 1272625408 10092
## - Age                               1      91106 1272670755 10092
## - YearsSinceLastPromotion           1     113651 1272693300 10092
## - StockOptionLevel                  1     160716 1272740364 10092
## - WorkLifeBalance                   1     251967 1272831616 10092
## - Education                         1     330560 1272910208 10092
## - EducationFieldMedical             1     361816 1272941465 10092
## - JobSatisfaction                   1     471504 1273051153 10092
## - NumCompaniesWorked                1     542092 1273121741 10092
## - EducationFieldOther               1     702744 1273282393 10092
## - OverTimeYes                       1     726499 1273306148 10092
## - RelationshipSatisfaction          1     782670 1273362319 10092
## - `EducationFieldTechnical Degree`  1     953507 1273533156 10092
## - `EducationFieldLife Sciences`     1    1004299 1273583947 10092
## - TrainingTimesLastYear             1    1883925 1274463574 10093
## - GenderMale                        1    2248806 1274828454 10093
## - MaritalStatusMarried              1    2896486 1275476135 10093
## - PercentSalaryHike                 1    3077666 1275657315 10093
## - EducationFieldMarketing           1    3180672 1275760320 10093
## - DistanceFromHome                  1    3427796 1276007445 10094
## - EnvironmentSatisfaction           1    3653420 1276233068 10094
## <none>                                           1272579649 10094
## - JobInvolvement                    1    5897839 1278477487 10095
## - BusinessTravelTravel_Rarely       1   10004252 1282583900 10097
## - YearsWithCurrManager              1   10456969 1283036618 10097
## - TotalWorkingYears                 1   33161430 1305741079 10110
## - JobLevel                          1 4693764209 5966343857 11167
## 
## Step:  AIC=10091.6
## .outcome ~ Age + BusinessTravelTravel_Frequently + BusinessTravelTravel_Rarely + 
##     DistanceFromHome + Education + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     GenderMale + JobInvolvement + JobLevel + JobSatisfaction + 
##     MaritalStatusMarried + NumCompaniesWorked + OverTimeYes + 
##     PercentSalaryHike + RelationshipSatisfaction + StockOptionLevel + 
##     TotalWorkingYears + TrainingTimesLastYear + WorkLifeBalance + 
##     YearsInCurrentRole + YearsSinceLastPromotion + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - YearsInCurrentRole                1      41317 1272621769 10090
## - BusinessTravelTravel_Frequently   1      46485 1272626937 10090
## - Age                               1      92839 1272673291 10090
## - YearsSinceLastPromotion           1     113846 1272694298 10090
## - WorkLifeBalance                   1     251732 1272832184 10090
## - StockOptionLevel                  1     283807 1272864259 10090
## - Education                         1     331869 1272912321 10090
## - EducationFieldMedical             1     361645 1272942097 10090
## - JobSatisfaction                   1     476759 1273057211 10090
## - NumCompaniesWorked                1     545246 1273125698 10090
## - EducationFieldOther               1     702356 1273282808 10090
## - OverTimeYes                       1     726762 1273307214 10090
## - RelationshipSatisfaction          1     784097 1273364549 10090
## - `EducationFieldTechnical Degree`  1     953125 1273533577 10090
## - `EducationFieldLife Sciences`     1    1003578 1273584030 10090
## - TrainingTimesLastYear             1    1891949 1274472401 10091
## - GenderMale                        1    2264368 1274844820 10091
## - PercentSalaryHike                 1    3080852 1275661304 10091
## - EducationFieldMarketing           1    3180172 1275760624 10091
## - DistanceFromHome                  1    3427277 1276007729 10092
## - EnvironmentSatisfaction           1    3660323 1276240775 10092
## <none>                                           1272580452 10092
## - MaritalStatusMarried              1    4783378 1277363830 10092
## - JobInvolvement                    1    5897204 1278477656 10093
## - BusinessTravelTravel_Rarely       1   10036208 1282616660 10095
## - YearsWithCurrManager              1   10478730 1283059182 10095
## - TotalWorkingYears                 1   33163402 1305743854 10108
## - JobLevel                          1 4695773214 5968353666 11165
## 
## Step:  AIC=10089.62
## .outcome ~ Age + BusinessTravelTravel_Frequently + BusinessTravelTravel_Rarely + 
##     DistanceFromHome + Education + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     GenderMale + JobInvolvement + JobLevel + JobSatisfaction + 
##     MaritalStatusMarried + NumCompaniesWorked + OverTimeYes + 
##     PercentSalaryHike + RelationshipSatisfaction + StockOptionLevel + 
##     TotalWorkingYears + TrainingTimesLastYear + WorkLifeBalance + 
##     YearsSinceLastPromotion + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - BusinessTravelTravel_Frequently   1      49153 1272670921 10088
## - Age                               1      86306 1272708075 10088
## - YearsSinceLastPromotion           1     164442 1272786211 10088
## - WorkLifeBalance                   1     266136 1272887904 10088
## - StockOptionLevel                  1     301418 1272923187 10088
## - Education                         1     337003 1272958772 10088
## - EducationFieldMedical             1     363688 1272985457 10088
## - JobSatisfaction                   1     478173 1273099942 10088
## - NumCompaniesWorked                1     598612 1273220380 10088
## - EducationFieldOther               1     708714 1273330482 10088
## - OverTimeYes                       1     718394 1273340163 10088
## - RelationshipSatisfaction          1     768620 1273390389 10088
## - `EducationFieldTechnical Degree`  1     970784 1273592553 10088
## - `EducationFieldLife Sciences`     1    1007659 1273629427 10088
## - TrainingTimesLastYear             1    1911264 1274533032 10089
## - GenderMale                        1    2295577 1274917345 10089
## - PercentSalaryHike                 1    3051350 1275673119 10089
## - EducationFieldMarketing           1    3191325 1275813093 10089
## - DistanceFromHome                  1    3418054 1276039823 10090
## <none>                                           1272621769 10090
## - EnvironmentSatisfaction           1    3689027 1276310796 10090
## - MaritalStatusMarried              1    4742241 1277364010 10090
## - JobInvolvement                    1    5870795 1278492564 10091
## - BusinessTravelTravel_Rarely       1   10046621 1282668389 10093
## - YearsWithCurrManager              1   15259738 1287881507 10096
## - TotalWorkingYears                 1   33604032 1306225800 10106
## - JobLevel                          1 4695799056 5968420824 11163
## 
## Step:  AIC=10087.65
## .outcome ~ Age + BusinessTravelTravel_Rarely + DistanceFromHome + 
##     Education + `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     JobSatisfaction + MaritalStatusMarried + NumCompaniesWorked + 
##     OverTimeYes + PercentSalaryHike + RelationshipSatisfaction + 
##     StockOptionLevel + TotalWorkingYears + TrainingTimesLastYear + 
##     WorkLifeBalance + YearsSinceLastPromotion + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - Age                               1      92612 1272763534 10086
## - YearsSinceLastPromotion           1     164398 1272835320 10086
## - WorkLifeBalance                   1     267807 1272938728 10086
## - StockOptionLevel                  1     314678 1272985599 10086
## - Education                         1     338705 1273009626 10086
## - EducationFieldMedical             1     361348 1273032269 10086
## - JobSatisfaction                   1     497652 1273168573 10086
## - NumCompaniesWorked                1     586980 1273257901 10086
## - EducationFieldOther               1     696189 1273367110 10086
## - OverTimeYes                       1     756257 1273427178 10086
## - RelationshipSatisfaction          1     799957 1273470879 10086
## - `EducationFieldTechnical Degree`  1     963400 1273634322 10086
## - `EducationFieldLife Sciences`     1     998033 1273668955 10086
## - TrainingTimesLastYear             1    1952671 1274623592 10087
## - GenderMale                        1    2272797 1274943718 10087
## - PercentSalaryHike                 1    3031800 1275702722 10087
## - EducationFieldMarketing           1    3172855 1275843776 10087
## - DistanceFromHome                  1    3434447 1276105368 10088
## <none>                                           1272670921 10088
## - EnvironmentSatisfaction           1    3691708 1276362629 10088
## - MaritalStatusMarried              1    4754411 1277425333 10088
## - JobInvolvement                    1    5986647 1278657569 10089
## - YearsWithCurrManager              1   15313557 1287984479 10094
## - BusinessTravelTravel_Rarely       1   19529761 1292200683 10096
## - TotalWorkingYears                 1   34042222 1306713144 10104
## - JobLevel                          1 4700712282 5973383203 11162
## 
## Step:  AIC=10085.7
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + Education + 
##     `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     JobSatisfaction + MaritalStatusMarried + NumCompaniesWorked + 
##     OverTimeYes + PercentSalaryHike + RelationshipSatisfaction + 
##     StockOptionLevel + TotalWorkingYears + TrainingTimesLastYear + 
##     WorkLifeBalance + YearsSinceLastPromotion + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - YearsSinceLastPromotion           1     159269 1272922802 10084
## - WorkLifeBalance                   1     256134 1273019668 10084
## - Education                         1     291761 1273055295 10084
## - StockOptionLevel                  1     318699 1273082233 10084
## - EducationFieldMedical             1     362503 1273126037 10084
## - JobSatisfaction                   1     484710 1273248244 10084
## - NumCompaniesWorked                1     555005 1273318538 10084
## - EducationFieldOther               1     690583 1273454116 10084
## - OverTimeYes                       1     742736 1273506269 10084
## - RelationshipSatisfaction          1     791805 1273555339 10084
## - `EducationFieldTechnical Degree`  1     978836 1273742370 10084
## - `EducationFieldLife Sciences`     1    1013086 1273776619 10084
## - TrainingTimesLastYear             1    1921513 1274685047 10085
## - GenderMale                        1    2254178 1275017712 10085
## - PercentSalaryHike                 1    3034578 1275798112 10085
## - EducationFieldMarketing           1    3226708 1275990241 10086
## - DistanceFromHome                  1    3425888 1276189422 10086
## <none>                                           1272763534 10086
## - EnvironmentSatisfaction           1    3725192 1276488725 10086
## - MaritalStatusMarried              1    4711638 1277475172 10086
## - JobInvolvement                    1    5956811 1278720344 10087
## - YearsWithCurrManager              1   15277858 1288041391 10092
## - BusinessTravelTravel_Rarely       1   19730747 1292494280 10094
## - TotalWorkingYears                 1   42646831 1315410364 10107
## - JobLevel                          1 4739548556 6012312090 11164
## 
## Step:  AIC=10083.79
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + Education + 
##     `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     JobSatisfaction + MaritalStatusMarried + NumCompaniesWorked + 
##     OverTimeYes + PercentSalaryHike + RelationshipSatisfaction + 
##     StockOptionLevel + TotalWorkingYears + TrainingTimesLastYear + 
##     WorkLifeBalance + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - WorkLifeBalance                   1     278466 1273201268 10082
## - Education                         1     287998 1273210800 10082
## - StockOptionLevel                  1     310991 1273233793 10082
## - EducationFieldMedical             1     401510 1273324312 10082
## - JobSatisfaction                   1     492353 1273415155 10082
## - NumCompaniesWorked                1     651245 1273574047 10082
## - EducationFieldOther               1     715937 1273638739 10082
## - OverTimeYes                       1     745260 1273668062 10082
## - RelationshipSatisfaction          1     747618 1273670420 10082
## - `EducationFieldTechnical Degree`  1    1033122 1273955924 10082
## - `EducationFieldLife Sciences`     1    1065101 1273987903 10082
## - TrainingTimesLastYear             1    1989245 1274912047 10083
## - GenderMale                        1    2276524 1275199326 10083
## - PercentSalaryHike                 1    3183734 1276106537 10084
## - EducationFieldMarketing           1    3310878 1276233680 10084
## - DistanceFromHome                  1    3380922 1276303724 10084
## <none>                                           1272922802 10084
## - EnvironmentSatisfaction           1    3813703 1276736505 10084
## - MaritalStatusMarried              1    4647411 1277570213 10084
## - JobInvolvement                    1    6101303 1279024105 10085
## - YearsWithCurrManager              1   18865470 1291788272 10092
## - BusinessTravelTravel_Rarely       1   19928608 1292851410 10093
## - TotalWorkingYears                 1   43839311 1316762114 10105
## - JobLevel                          1 4755912343 6028835146 11164
## 
## Step:  AIC=10081.94
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + Education + 
##     `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     JobSatisfaction + MaritalStatusMarried + NumCompaniesWorked + 
##     OverTimeYes + PercentSalaryHike + RelationshipSatisfaction + 
##     StockOptionLevel + TotalWorkingYears + TrainingTimesLastYear + 
##     YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - Education                         1     297223 1273498491 10080
## - StockOptionLevel                  1     325499 1273526767 10080
## - EducationFieldMedical             1     384597 1273585865 10080
## - JobSatisfaction                   1     486585 1273687853 10080
## - NumCompaniesWorked                1     640015 1273841283 10080
## - RelationshipSatisfaction          1     716299 1273917567 10080
## - EducationFieldOther               1     717658 1273918926 10080
## - OverTimeYes                       1     721783 1273923051 10080
## - `EducationFieldTechnical Degree`  1    1032702 1274233970 10080
## - `EducationFieldLife Sciences`     1    1043582 1274244849 10080
## - TrainingTimesLastYear             1    1942231 1275143498 10081
## - GenderMale                        1    2283082 1275484350 10081
## - PercentSalaryHike                 1    3161562 1276362830 10082
## - EducationFieldMarketing           1    3305158 1276506426 10082
## - DistanceFromHome                  1    3340463 1276541731 10082
## <none>                                           1273201268 10082
## - EnvironmentSatisfaction           1    3958819 1277160087 10082
## - MaritalStatusMarried              1    4649065 1277850333 10082
## - JobInvolvement                    1    6053068 1279254336 10083
## - YearsWithCurrManager              1   18878196 1292079464 10090
## - BusinessTravelTravel_Rarely       1   20190235 1293391502 10091
## - TotalWorkingYears                 1   43829932 1317031200 10104
## - JobLevel                          1 4756105343 6029306611 11162
## 
## Step:  AIC=10080.1
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     GenderMale + JobInvolvement + JobLevel + JobSatisfaction + 
##     MaritalStatusMarried + NumCompaniesWorked + OverTimeYes + 
##     PercentSalaryHike + RelationshipSatisfaction + StockOptionLevel + 
##     TotalWorkingYears + TrainingTimesLastYear + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - StockOptionLevel                  1     317086 1273815577 10078
## - EducationFieldMedical             1     379797 1273878288 10078
## - JobSatisfaction                   1     523438 1274021929 10078
## - RelationshipSatisfaction          1     674963 1274173454 10078
## - EducationFieldOther               1     698397 1274196888 10078
## - OverTimeYes                       1     706926 1274205417 10078
## - NumCompaniesWorked                1     799024 1274297515 10078
## - `EducationFieldLife Sciences`     1    1020696 1274519187 10079
## - `EducationFieldTechnical Degree`  1    1027656 1274526147 10079
## - TrainingTimesLastYear             1    1852778 1275351269 10079
## - GenderMale                        1    2248821 1275747311 10079
## - PercentSalaryHike                 1    3141423 1276639914 10080
## - DistanceFromHome                  1    3206542 1276705033 10080
## - EducationFieldMarketing           1    3238719 1276737210 10080
## <none>                                           1273498491 10080
## - EnvironmentSatisfaction           1    4136656 1277635147 10080
## - MaritalStatusMarried              1    4628546 1278127037 10081
## - JobInvolvement                    1    6107060 1279605551 10081
## - YearsWithCurrManager              1   18622659 1292121150 10088
## - BusinessTravelTravel_Rarely       1   20193899 1293692390 10089
## - TotalWorkingYears                 1   43844588 1317343079 10102
## - JobLevel                          1 4763276527 6036775018 11161
## 
## Step:  AIC=10078.27
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     GenderMale + JobInvolvement + JobLevel + JobSatisfaction + 
##     MaritalStatusMarried + NumCompaniesWorked + OverTimeYes + 
##     PercentSalaryHike + RelationshipSatisfaction + TotalWorkingYears + 
##     TrainingTimesLastYear + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - EducationFieldMedical             1     355713 1274171290 10076
## - JobSatisfaction                   1     516729 1274332306 10077
## - EducationFieldOther               1     643123 1274458700 10077
## - OverTimeYes                       1     684557 1274500134 10077
## - RelationshipSatisfaction          1     707672 1274523249 10077
## - NumCompaniesWorked                1     758050 1274573627 10077
## - `EducationFieldTechnical Degree`  1     982662 1274798239 10077
## - `EducationFieldLife Sciences`     1     985992 1274801570 10077
## - TrainingTimesLastYear             1    1818226 1275633803 10077
## - GenderMale                        1    2204396 1276019973 10078
## - PercentSalaryHike                 1    3115867 1276931444 10078
## - EducationFieldMarketing           1    3197360 1277012937 10078
## - DistanceFromHome                  1    3282666 1277098243 10078
## <none>                                           1273815577 10078
## - EnvironmentSatisfaction           1    4268627 1278084204 10079
## - MaritalStatusMarried              1    4317487 1278133065 10079
## - JobInvolvement                    1    5920156 1279735733 10080
## - YearsWithCurrManager              1   18792858 1292608436 10086
## - BusinessTravelTravel_Rarely       1   20486435 1294302012 10087
## - TotalWorkingYears                 1   43914924 1317730502 10100
## - JobLevel                          1 4763235778 6037051355 11159
## 
## Step:  AIC=10076.47
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     JobSatisfaction + MaritalStatusMarried + NumCompaniesWorked + 
##     OverTimeYes + PercentSalaryHike + RelationshipSatisfaction + 
##     TotalWorkingYears + TrainingTimesLastYear + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - EducationFieldOther               1     318136 1274489426 10075
## - JobSatisfaction                   1     505829 1274677120 10075
## - OverTimeYes                       1     669144 1274840434 10075
## - NumCompaniesWorked                1     734378 1274905668 10075
## - RelationshipSatisfaction          1     761823 1274933113 10075
## - `EducationFieldTechnical Degree`  1     959056 1275130346 10075
## - TrainingTimesLastYear             1    1740221 1275911511 10075
## - `EducationFieldLife Sciences`     1    1836455 1276007745 10076
## - GenderMale                        1    2314399 1276485690 10076
## - PercentSalaryHike                 1    3104044 1277275334 10076
## - DistanceFromHome                  1    3399433 1277570724 10076
## <none>                                           1274171290 10076
## - EnvironmentSatisfaction           1    4199362 1278370653 10077
## - MaritalStatusMarried              1    4506572 1278677863 10077
## - JobInvolvement                    1    5899477 1280070767 10078
## - EducationFieldMarketing           1    8131533 1282302824 10079
## - YearsWithCurrManager              1   18944883 1293116173 10085
## - BusinessTravelTravel_Rarely       1   20427774 1294599064 10086
## - TotalWorkingYears                 1   44050926 1318222216 10098
## - JobLevel                          1 4766195076 6040366366 11158
## 
## Step:  AIC=10074.64
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     JobSatisfaction + MaritalStatusMarried + NumCompaniesWorked + 
##     OverTimeYes + PercentSalaryHike + RelationshipSatisfaction + 
##     TotalWorkingYears + TrainingTimesLastYear + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - JobSatisfaction                   1     504988 1274994414 10073
## - OverTimeYes                       1     657350 1275146777 10073
## - NumCompaniesWorked                1     745703 1275235129 10073
## - `EducationFieldTechnical Degree`  1     790590 1275280017 10073
## - RelationshipSatisfaction          1     818906 1275308332 10073
## - `EducationFieldLife Sciences`     1    1541153 1276030579 10074
## - TrainingTimesLastYear             1    1787660 1276277086 10074
## - GenderMale                        1    2228423 1276717849 10074
## - PercentSalaryHike                 1    3027965 1277517391 10074
## - DistanceFromHome                  1    3399856 1277889283 10074
## <none>                                           1274489426 10075
## - EnvironmentSatisfaction           1    4413500 1278902926 10075
## - MaritalStatusMarried              1    4577267 1279066693 10075
## - JobInvolvement                    1    5998862 1280488288 10076
## - EducationFieldMarketing           1    7815250 1282304677 10077
## - YearsWithCurrManager              1   19058485 1293547911 10083
## - BusinessTravelTravel_Rarely       1   20356264 1294845690 10084
## - TotalWorkingYears                 1   44749692 1319239119 10097
## - JobLevel                          1 4771801922 6046291348 11156
## 
## Step:  AIC=10072.92
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     MaritalStatusMarried + NumCompaniesWorked + OverTimeYes + 
##     PercentSalaryHike + RelationshipSatisfaction + TotalWorkingYears + 
##     TrainingTimesLastYear + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - NumCompaniesWorked                1     675230 1275669644 10071
## - OverTimeYes                       1     688562 1275682976 10071
## - RelationshipSatisfaction          1     774115 1275768529 10071
## - `EducationFieldTechnical Degree`  1     810846 1275805261 10071
## - `EducationFieldLife Sciences`     1    1544442 1276538857 10072
## - TrainingTimesLastYear             1    1717074 1276711489 10072
## - GenderMale                        1    2273045 1277267459 10072
## - PercentSalaryHike                 1    2978668 1277973082 10072
## - DistanceFromHome                  1    3475943 1278470358 10073
## <none>                                           1274994414 10073
## - EnvironmentSatisfaction           1    4471829 1279466243 10073
## - MaritalStatusMarried              1    4542820 1279537234 10073
## - JobInvolvement                    1    5840697 1280835111 10074
## - EducationFieldMarketing           1    7889436 1282883850 10075
## - YearsWithCurrManager              1   19020866 1294015280 10081
## - BusinessTravelTravel_Rarely       1   19971998 1294966412 10082
## - TotalWorkingYears                 1   44576151 1319570566 10095
## - JobLevel                          1 4771666699 6046661113 11154
## 
## Step:  AIC=10071.29
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     MaritalStatusMarried + OverTimeYes + PercentSalaryHike + 
##     RelationshipSatisfaction + TotalWorkingYears + TrainingTimesLastYear + 
##     YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - OverTimeYes                       1     687399 1276357043 10070
## - `EducationFieldTechnical Degree`  1     720330 1276389974 10070
## - RelationshipSatisfaction          1     883694 1276553338 10070
## - `EducationFieldLife Sciences`     1    1477692 1277147336 10070
## - TrainingTimesLastYear             1    1594722 1277264366 10070
## - GenderMale                        1    2203993 1277873637 10070
## - PercentSalaryHike                 1    3046019 1278715663 10071
## <none>                                           1275669644 10071
## - DistanceFromHome                  1    3824167 1279493811 10071
## - EnvironmentSatisfaction           1    4421273 1280090917 10072
## - MaritalStatusMarried              1    4575756 1280245400 10072
## - JobInvolvement                    1    5825838 1281495482 10072
## - EducationFieldMarketing           1    8000670 1283670314 10074
## - BusinessTravelTravel_Rarely       1   19686425 1295356069 10080
## - YearsWithCurrManager              1   22816741 1298486385 10082
## - TotalWorkingYears                 1   54566973 1330236617 10098
## - JobLevel                          1 4825604376 6101274020 11158
## 
## Step:  AIC=10069.66
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     MaritalStatusMarried + PercentSalaryHike + RelationshipSatisfaction + 
##     TotalWorkingYears + TrainingTimesLastYear + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - `EducationFieldTechnical Degree`  1     739800 1277096843 10068
## - RelationshipSatisfaction          1     890381 1277247425 10068
## - TrainingTimesLastYear             1    1509667 1277866710 10068
## - `EducationFieldLife Sciences`     1    1521186 1277878230 10068
## - GenderMale                        1    2139645 1278496688 10069
## - PercentSalaryHike                 1    3013346 1279370390 10069
## - DistanceFromHome                  1    3624608 1279981652 10070
## <none>                                           1276357043 10070
## - EnvironmentSatisfaction           1    4224553 1280581596 10070
## - MaritalStatusMarried              1    4486977 1280844020 10070
## - JobInvolvement                    1    5605992 1281963036 10071
## - EducationFieldMarketing           1    8110400 1284467444 10072
## - BusinessTravelTravel_Rarely       1   19746734 1296103777 10078
## - YearsWithCurrManager              1   23275053 1299632097 10080
## - TotalWorkingYears                 1   54724871 1331081914 10097
## - JobLevel                          1 4828041894 6104398937 11157
## 
## Step:  AIC=10068.06
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EnvironmentSatisfaction + GenderMale + 
##     JobInvolvement + JobLevel + MaritalStatusMarried + PercentSalaryHike + 
##     RelationshipSatisfaction + TotalWorkingYears + TrainingTimesLastYear + 
##     YearsWithCurrManager
## 
##                                 Df  Sum of Sq        RSS   AIC
## - RelationshipSatisfaction       1     970697 1278067540 10067
## - `EducationFieldLife Sciences`  1    1048076 1278144919 10067
## - TrainingTimesLastYear          1    1488123 1278584967 10067
## - GenderMale                     1    2162603 1279259447 10067
## - PercentSalaryHike              1    3256111 1280352954 10068
## - DistanceFromHome               1    3511479 1280608322 10068
## <none>                                        1277096843 10068
## - EnvironmentSatisfaction        1    4323295 1281420139 10068
## - MaritalStatusMarried           1    4338481 1281435325 10068
## - JobInvolvement                 1    5657576 1282754420 10069
## - EducationFieldMarketing        1    7469785 1284566628 10070
## - BusinessTravelTravel_Rarely    1   19656805 1296753649 10077
## - YearsWithCurrManager           1   23443550 1300540393 10079
## - TotalWorkingYears              1   55128332 1332225176 10096
## - JobLevel                       1 4829917593 6107014436 11155
## 
## Step:  AIC=10066.59
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EnvironmentSatisfaction + GenderMale + 
##     JobInvolvement + JobLevel + MaritalStatusMarried + PercentSalaryHike + 
##     TotalWorkingYears + TrainingTimesLastYear + YearsWithCurrManager
## 
##                                 Df  Sum of Sq        RSS   AIC
## - `EducationFieldLife Sciences`  1    1134334 1279201874 10065
## - TrainingTimesLastYear          1    1508542 1279576083 10065
## - GenderMale                     1    2184582 1280252122 10066
## - PercentSalaryHike              1    3002322 1281069862 10066
## - DistanceFromHome               1    3313049 1281380589 10066
## <none>                                        1278067540 10067
## - MaritalStatusMarried           1    4091679 1282159219 10067
## - EnvironmentSatisfaction        1    4519680 1282587220 10067
## - JobInvolvement                 1    5661129 1283728669 10068
## - EducationFieldMarketing        1    7392825 1285460365 10069
## - BusinessTravelTravel_Rarely    1   19101742 1297169282 10075
## - YearsWithCurrManager           1   23582146 1301649686 10077
## - TotalWorkingYears              1   54886079 1332953619 10094
## - JobLevel                       1 4830537645 6108605185 11153
## 
## Step:  AIC=10065.21
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + EducationFieldMarketing + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     MaritalStatusMarried + PercentSalaryHike + TotalWorkingYears + 
##     TrainingTimesLastYear + YearsWithCurrManager
## 
##                               Df  Sum of Sq        RSS   AIC
## - TrainingTimesLastYear        1    1873333 1281075207 10064
## - GenderMale                   1    2143928 1281345802 10064
## - PercentSalaryHike            1    2879072 1282080946 10065
## - DistanceFromHome             1    3223735 1282425609 10065
## <none>                                      1279201874 10065
## - MaritalStatusMarried         1    4026307 1283228181 10065
## - EnvironmentSatisfaction      1    4385870 1283587745 10066
## - JobInvolvement               1    5633767 1284835642 10066
## - EducationFieldMarketing      1    6326204 1285528078 10067
## - BusinessTravelTravel_Rarely  1   18917543 1298119417 10073
## - YearsWithCurrManager         1   23665837 1302867711 10076
## - TotalWorkingYears            1   55148621 1334350495 10093
## - JobLevel                     1 4829895313 6109097187 11151
## 
## Step:  AIC=10064.23
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + EducationFieldMarketing + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     MaritalStatusMarried + PercentSalaryHike + TotalWorkingYears + 
##     YearsWithCurrManager
## 
##                               Df  Sum of Sq        RSS   AIC
## - GenderMale                   1    1999053 1283074261 10063
## - PercentSalaryHike            1    2976640 1284051848 10064
## - DistanceFromHome             1    3296476 1284371683 10064
## <none>                                      1281075207 10064
## - MaritalStatusMarried         1    3848155 1284923363 10064
## - EnvironmentSatisfaction      1    4506837 1285582044 10065
## - JobInvolvement               1    5366677 1286441884 10065
## - EducationFieldMarketing      1    6710482 1287785690 10066
## - BusinessTravelTravel_Rarely  1   19017145 1300092352 10072
## - YearsWithCurrManager         1   23351983 1304427190 10075
## - TotalWorkingYears            1   55003567 1336078774 10092
## - JobLevel                     1 4828166690 6109241897 11150
## 
## Step:  AIC=10063.31
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + EducationFieldMarketing + 
##     EnvironmentSatisfaction + JobInvolvement + JobLevel + MaritalStatusMarried + 
##     PercentSalaryHike + TotalWorkingYears + YearsWithCurrManager
## 
##                               Df  Sum of Sq        RSS   AIC
## - PercentSalaryHike            1    3005019 1286079280 10063
## - DistanceFromHome             1    3433835 1286508096 10063
## <none>                                      1283074261 10063
## - MaritalStatusMarried         1    3744231 1286818492 10063
## - EnvironmentSatisfaction      1    4463400 1287537661 10064
## - JobInvolvement               1    5386957 1288461218 10064
## - EducationFieldMarketing      1    6921197 1289995458 10065
## - BusinessTravelTravel_Rarely  1   19106484 1302180745 10072
## - YearsWithCurrManager         1   24131690 1307205951 10074
## - TotalWorkingYears            1   54297250 1337371511 10090
## - JobLevel                     1 4827130867 6110205128 11148
## 
## Step:  AIC=10062.94
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + EducationFieldMarketing + 
##     EnvironmentSatisfaction + JobInvolvement + JobLevel + MaritalStatusMarried + 
##     TotalWorkingYears + YearsWithCurrManager
## 
##                               Df  Sum of Sq        RSS   AIC
## - DistanceFromHome             1    3125327 1289204606 10063
## <none>                                      1286079280 10063
## - MaritalStatusMarried         1    3716662 1289795941 10063
## - EnvironmentSatisfaction      1    4470258 1290549538 10063
## - JobInvolvement               1    5461229 1291540509 10064
## - EducationFieldMarketing      1    7304860 1293384140 10065
## - BusinessTravelTravel_Rarely  1   19305033 1305384313 10071
## - YearsWithCurrManager         1   25155596 1311234876 10074
## - TotalWorkingYears            1   53829457 1339908736 10090
## - JobLevel                     1 4824125898 6110205178 11146
## 
## Step:  AIC=10062.63
## .outcome ~ BusinessTravelTravel_Rarely + EducationFieldMarketing + 
##     EnvironmentSatisfaction + JobInvolvement + JobLevel + MaritalStatusMarried + 
##     TotalWorkingYears + YearsWithCurrManager
## 
##                               Df  Sum of Sq        RSS   AIC
## - MaritalStatusMarried         1    3011741 1292216347 10062
## <none>                                      1289204606 10063
## - EnvironmentSatisfaction      1    4241635 1293446241 10063
## - JobInvolvement               1    5315113 1294519719 10064
## - EducationFieldMarketing      1    7932419 1297137026 10065
## - BusinessTravelTravel_Rarely  1   21239332 1310443938 10072
## - YearsWithCurrManager         1   24549980 1313754586 10074
## - TotalWorkingYears            1   53658688 1342863295 10089
## - JobLevel                     1 4821384504 6110589110 11144
## 
## Step:  AIC=10062.26
## .outcome ~ BusinessTravelTravel_Rarely + EducationFieldMarketing + 
##     EnvironmentSatisfaction + JobInvolvement + JobLevel + TotalWorkingYears + 
##     YearsWithCurrManager
## 
##                               Df  Sum of Sq        RSS   AIC
## <none>                                      1292216347 10062
## - EnvironmentSatisfaction      1    4721008 1296937355 10063
## - JobInvolvement               1    5345273 1297561620 10063
## - EducationFieldMarketing      1    8632450 1300848797 10065
## - BusinessTravelTravel_Rarely  1   22591665 1314808012 10072
## - YearsWithCurrManager         1   24141762 1316358109 10073
## - TotalWorkingYears            1   53929544 1346145891 10089
## - JobLevel                     1 4838865566 6131081913 11144
## Start:  AIC=10076.26
## .outcome ~ Age + BusinessTravelTravel_Frequently + BusinessTravelTravel_Rarely + 
##     DistanceFromHome + Education + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     GenderMale + JobInvolvement + JobLevel + JobSatisfaction + 
##     MaritalStatusMarried + MaritalStatusSingle + NumCompaniesWorked + 
##     OverTimeYes + PercentSalaryHike + RelationshipSatisfaction + 
##     StockOptionLevel + TotalWorkingYears + TrainingTimesLastYear + 
##     WorkLifeBalance + YearsInCurrentRole + YearsSinceLastPromotion + 
##     YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - Age                               1       5161 1241277721 10074
## - YearsSinceLastPromotion           1      19100 1241291661 10074
## - StockOptionLevel                  1      27524 1241300085 10074
## - JobSatisfaction                   1      49673 1241322234 10074
## - BusinessTravelTravel_Frequently   1      74136 1241346697 10074
## - TrainingTimesLastYear             1     132405 1241404966 10074
## - PercentSalaryHike                 1     152440 1241425001 10074
## - Education                         1     167083 1241439643 10074
## - `EducationFieldTechnical Degree`  1     180004 1241452565 10074
## - OverTimeYes                       1     253000 1241525560 10074
## - WorkLifeBalance                   1     265019 1241537580 10074
## - MaritalStatusSingle               1     469286 1241741846 10074
## - NumCompaniesWorked                1     702244 1241974805 10075
## - `EducationFieldLife Sciences`     1    1136365 1242408926 10075
## - EducationFieldMedical             1    1194832 1242467393 10075
## - RelationshipSatisfaction          1    1262983 1242535544 10075
## - JobInvolvement                    1    1332152 1242604713 10075
## - EducationFieldOther               1    1705873 1242978433 10075
## - MaritalStatusMarried              1    1965893 1243238453 10075
## - EnvironmentSatisfaction           1    2511789 1243784350 10076
## - GenderMale                        1    2718608 1243991169 10076
## <none>                                           1241272561 10076
## - EducationFieldMarketing           1    4739212 1246011772 10077
## - YearsInCurrentRole                1    6284565 1247557125 10078
## - YearsWithCurrManager              1    7506380 1248778940 10078
## - BusinessTravelTravel_Rarely       1    9590810 1250863371 10080
## - DistanceFromHome                  1   14895485 1256168046 10083
## - TotalWorkingYears                 1   43820802 1285093363 10098
## - JobLevel                          1 3937007939 5178280499 11068
## 
## Step:  AIC=10074.26
## .outcome ~ BusinessTravelTravel_Frequently + BusinessTravelTravel_Rarely + 
##     DistanceFromHome + Education + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     GenderMale + JobInvolvement + JobLevel + JobSatisfaction + 
##     MaritalStatusMarried + MaritalStatusSingle + NumCompaniesWorked + 
##     OverTimeYes + PercentSalaryHike + RelationshipSatisfaction + 
##     StockOptionLevel + TotalWorkingYears + TrainingTimesLastYear + 
##     WorkLifeBalance + YearsInCurrentRole + YearsSinceLastPromotion + 
##     YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - YearsSinceLastPromotion           1      19257 1241296979 10072
## - StockOptionLevel                  1      26378 1241304099 10072
## - JobSatisfaction                   1      49373 1241327094 10072
## - BusinessTravelTravel_Frequently   1      74625 1241352346 10072
## - TrainingTimesLastYear             1     132635 1241410356 10072
## - PercentSalaryHike                 1     151091 1241428813 10072
## - Education                         1     161975 1241439696 10072
## - `EducationFieldTechnical Degree`  1     182358 1241460079 10072
## - OverTimeYes                       1     249521 1241527242 10072
## - WorkLifeBalance                   1     261850 1241539572 10072
## - MaritalStatusSingle               1     486300 1241764021 10072
## - NumCompaniesWorked                1     723038 1242000759 10073
## - `EducationFieldLife Sciences`     1    1145331 1242423053 10073
## - EducationFieldMedical             1    1200461 1242478182 10073
## - RelationshipSatisfaction          1    1261783 1242539504 10073
## - JobInvolvement                    1    1328605 1242606326 10073
## - EducationFieldOther               1    1705825 1242983547 10073
## - MaritalStatusMarried              1    1975617 1243253339 10073
## - EnvironmentSatisfaction           1    2510325 1243788046 10074
## - GenderMale                        1    2717967 1243995688 10074
## <none>                                           1241277721 10074
## - EducationFieldMarketing           1    4769337 1246047059 10075
## - YearsInCurrentRole                1    6283874 1247561596 10076
## - YearsWithCurrManager              1    7519342 1248797064 10076
## - BusinessTravelTravel_Rarely       1    9609813 1250887534 10078
## - DistanceFromHome                  1   14890339 1256168061 10081
## - TotalWorkingYears                 1   55991868 1297269589 10103
## - JobLevel                          1 3954815436 5196093157 11069
## 
## Step:  AIC=10072.27
## .outcome ~ BusinessTravelTravel_Frequently + BusinessTravelTravel_Rarely + 
##     DistanceFromHome + Education + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     GenderMale + JobInvolvement + JobLevel + JobSatisfaction + 
##     MaritalStatusMarried + MaritalStatusSingle + NumCompaniesWorked + 
##     OverTimeYes + PercentSalaryHike + RelationshipSatisfaction + 
##     StockOptionLevel + TotalWorkingYears + TrainingTimesLastYear + 
##     WorkLifeBalance + YearsInCurrentRole + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - StockOptionLevel                  1      29197 1241326175 10070
## - JobSatisfaction                   1      46872 1241343850 10070
## - BusinessTravelTravel_Frequently   1      76645 1241373624 10070
## - TrainingTimesLastYear             1     129864 1241426843 10070
## - PercentSalaryHike                 1     145843 1241442821 10070
## - Education                         1     164646 1241461625 10070
## - `EducationFieldTechnical Degree`  1     175798 1241472777 10070
## - OverTimeYes                       1     251908 1241548887 10070
## - WorkLifeBalance                   1     258864 1241555843 10070
## - MaritalStatusSingle               1     479552 1241776530 10070
## - NumCompaniesWorked                1     753827 1242050806 10071
## - `EducationFieldLife Sciences`     1    1131414 1242428392 10071
## - EducationFieldMedical             1    1184008 1242480986 10071
## - RelationshipSatisfaction          1    1273734 1242570713 10071
## - JobInvolvement                    1    1316202 1242613181 10071
## - EducationFieldOther               1    1695834 1242992812 10071
## - MaritalStatusMarried              1    1972316 1243269295 10071
## - EnvironmentSatisfaction           1    2509888 1243806866 10072
## - GenderMale                        1    2732566 1244029545 10072
## <none>                                           1241296979 10072
## - EducationFieldMarketing           1    4750080 1246047059 10073
## - YearsInCurrentRole                1    6577989 1247874967 10074
## - YearsWithCurrManager              1    7552095 1248849073 10074
## - BusinessTravelTravel_Rarely       1    9592592 1250889570 10076
## - DistanceFromHome                  1   14907895 1256204873 10079
## - TotalWorkingYears                 1   58657052 1299954030 10102
## - JobLevel                          1 3987365930 5228662909 11071
## 
## Step:  AIC=10070.29
## .outcome ~ BusinessTravelTravel_Frequently + BusinessTravelTravel_Rarely + 
##     DistanceFromHome + Education + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     GenderMale + JobInvolvement + JobLevel + JobSatisfaction + 
##     MaritalStatusMarried + MaritalStatusSingle + NumCompaniesWorked + 
##     OverTimeYes + PercentSalaryHike + RelationshipSatisfaction + 
##     TotalWorkingYears + TrainingTimesLastYear + WorkLifeBalance + 
##     YearsInCurrentRole + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - JobSatisfaction                   1      44203 1241370378 10068
## - BusinessTravelTravel_Frequently   1      72251 1241398426 10068
## - TrainingTimesLastYear             1     127452 1241453627 10068
## - PercentSalaryHike                 1     149175 1241475351 10068
## - Education                         1     161268 1241487443 10068
## - `EducationFieldTechnical Degree`  1     177644 1241503819 10068
## - OverTimeYes                       1     251189 1241577364 10068
## - WorkLifeBalance                   1     269574 1241595750 10068
## - NumCompaniesWorked                1     777962 1242104137 10069
## - MaritalStatusSingle               1    1077835 1242404010 10069
## - `EducationFieldLife Sciences`     1    1142383 1242468558 10069
## - EducationFieldMedical             1    1191292 1242517467 10069
## - RelationshipSatisfaction          1    1281353 1242607529 10069
## - JobInvolvement                    1    1306480 1242632655 10069
## - EducationFieldOther               1    1688900 1243015075 10069
## - MaritalStatusMarried              1    2298482 1243624658 10070
## - EnvironmentSatisfaction           1    2511853 1243838028 10070
## - GenderMale                        1    2735155 1244061330 10070
## <none>                                           1241326175 10070
## - EducationFieldMarketing           1    4764235 1246090411 10071
## - YearsInCurrentRole                1    6638763 1247964938 10072
## - YearsWithCurrManager              1    7593503 1248919678 10072
## - BusinessTravelTravel_Rarely       1    9564356 1250890531 10074
## - DistanceFromHome                  1   15061944 1256388119 10077
## - TotalWorkingYears                 1   58986756 1300312932 10101
## - JobLevel                          1 3987550770 5228876945 11069
## 
## Step:  AIC=10068.32
## .outcome ~ BusinessTravelTravel_Frequently + BusinessTravelTravel_Rarely + 
##     DistanceFromHome + Education + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     GenderMale + JobInvolvement + JobLevel + MaritalStatusMarried + 
##     MaritalStatusSingle + NumCompaniesWorked + OverTimeYes + 
##     PercentSalaryHike + RelationshipSatisfaction + TotalWorkingYears + 
##     TrainingTimesLastYear + WorkLifeBalance + YearsInCurrentRole + 
##     YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - BusinessTravelTravel_Frequently   1      80566 1241450945 10066
## - TrainingTimesLastYear             1     122092 1241492470 10066
## - PercentSalaryHike                 1     149940 1241520318 10066
## - Education                         1     170113 1241540491 10066
## - `EducationFieldTechnical Degree`  1     172626 1241543004 10066
## - OverTimeYes                       1     261965 1241632343 10066
## - WorkLifeBalance                   1     278625 1241649003 10066
## - NumCompaniesWorked                1     805296 1242175674 10067
## - MaritalStatusSingle               1    1103006 1242473385 10067
## - `EducationFieldLife Sciences`     1    1120131 1242490509 10067
## - EducationFieldMedical             1    1171667 1242542046 10067
## - RelationshipSatisfaction          1    1271709 1242642087 10067
## - JobInvolvement                    1    1280729 1242651107 10067
## - EducationFieldOther               1    1669290 1243039669 10067
## - MaritalStatusMarried              1    2330070 1243700449 10068
## - EnvironmentSatisfaction           1    2524260 1243894638 10068
## - GenderMale                        1    2786678 1244157057 10068
## <none>                                           1241370378 10068
## - EducationFieldMarketing           1    4733697 1246104075 10069
## - YearsInCurrentRole                1    6641135 1248011513 10070
## - YearsWithCurrManager              1    7575551 1248945929 10071
## - BusinessTravelTravel_Rarely       1    9572381 1250942759 10072
## - DistanceFromHome                  1   15147311 1256517689 10075
## - TotalWorkingYears                 1   58947155 1300317534 10099
## - JobLevel                          1 3987903650 5229274029 11067
## 
## Step:  AIC=10066.36
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + Education + 
##     `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     MaritalStatusMarried + MaritalStatusSingle + NumCompaniesWorked + 
##     OverTimeYes + PercentSalaryHike + RelationshipSatisfaction + 
##     TotalWorkingYears + TrainingTimesLastYear + WorkLifeBalance + 
##     YearsInCurrentRole + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - TrainingTimesLastYear             1     131566 1241582510 10064
## - PercentSalaryHike                 1     158726 1241609671 10064
## - Education                         1     171306 1241622251 10064
## - `EducationFieldTechnical Degree`  1     174368 1241625312 10064
## - WorkLifeBalance                   1     271545 1241722490 10064
## - OverTimeYes                       1     273938 1241724882 10064
## - NumCompaniesWorked                1     826004 1242276949 10065
## - `EducationFieldLife Sciences`     1    1130636 1242581581 10065
## - MaritalStatusSingle               1    1178294 1242629239 10065
## - EducationFieldMedical             1    1192977 1242643922 10065
## - JobInvolvement                    1    1311527 1242762472 10065
## - RelationshipSatisfaction          1    1314474 1242765419 10065
## - EducationFieldOther               1    1702821 1243153766 10065
## - MaritalStatusMarried              1    2419806 1243870750 10066
## - EnvironmentSatisfaction           1    2520513 1243971458 10066
## - GenderMale                        1    2758059 1244209004 10066
## <none>                                           1241450945 10066
## - EducationFieldMarketing           1    4746071 1246197016 10067
## - YearsInCurrentRole                1    6642715 1248093660 10068
## - YearsWithCurrManager              1    7600508 1249051453 10069
## - DistanceFromHome                  1   15304439 1256755383 10073
## - BusinessTravelTravel_Rarely       1   18978844 1260429789 10075
## - TotalWorkingYears                 1   59247671 1300698616 10097
## - JobLevel                          1 3989653454 5231104399 11065
## 
## Step:  AIC=10064.43
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + Education + 
##     `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     MaritalStatusMarried + MaritalStatusSingle + NumCompaniesWorked + 
##     OverTimeYes + PercentSalaryHike + RelationshipSatisfaction + 
##     TotalWorkingYears + WorkLifeBalance + YearsInCurrentRole + 
##     YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - PercentSalaryHike                 1     156842 1241739352 10062
## - `EducationFieldTechnical Degree`  1     163509 1241746020 10062
## - Education                         1     164797 1241747307 10062
## - OverTimeYes                       1     252730 1241835240 10063
## - WorkLifeBalance                   1     260720 1241843231 10063
## - NumCompaniesWorked                1     857747 1242440257 10063
## - `EducationFieldLife Sciences`     1    1119502 1242702013 10063
## - MaritalStatusSingle               1    1153473 1242735983 10063
## - EducationFieldMedical             1    1155936 1242738447 10063
## - JobInvolvement                    1    1292702 1242875213 10063
## - RelationshipSatisfaction          1    1340281 1242922792 10063
## - EducationFieldOther               1    1696843 1243279354 10063
## - MaritalStatusMarried              1    2383130 1243965641 10064
## - EnvironmentSatisfaction           1    2545734 1244128244 10064
## - GenderMale                        1    2738586 1244321096 10064
## <none>                                           1241582510 10064
## - EducationFieldMarketing           1    4719837 1246302347 10065
## - YearsInCurrentRole                1    6692297 1248274807 10066
## - YearsWithCurrManager              1    7555024 1249137535 10067
## - DistanceFromHome                  1   15396927 1256979437 10071
## - BusinessTravelTravel_Rarely       1   18951276 1260533787 10073
## - TotalWorkingYears                 1   59137533 1300720044 10095
## - JobLevel                          1 3994815712 5236398223 11064
## 
## Step:  AIC=10062.52
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + Education + 
##     `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     MaritalStatusMarried + MaritalStatusSingle + NumCompaniesWorked + 
##     OverTimeYes + RelationshipSatisfaction + TotalWorkingYears + 
##     WorkLifeBalance + YearsInCurrentRole + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - Education                         1     163843 1241903195 10061
## - `EducationFieldTechnical Degree`  1     164289 1241903641 10061
## - WorkLifeBalance                   1     252203 1241991556 10061
## - OverTimeYes                       1     255683 1241995035 10061
## - NumCompaniesWorked                1     865635 1242604988 10061
## - `EducationFieldLife Sciences`     1    1093709 1242833061 10061
## - EducationFieldMedical             1    1126832 1242866185 10061
## - MaritalStatusSingle               1    1157290 1242896642 10061
## - JobInvolvement                    1    1303424 1243042776 10061
## - RelationshipSatisfaction          1    1310541 1243049893 10061
## - EducationFieldOther               1    1650101 1243389453 10061
## - MaritalStatusMarried              1    2366218 1244105570 10062
## - EnvironmentSatisfaction           1    2531940 1244271292 10062
## - GenderMale                        1    2764045 1244503397 10062
## <none>                                           1241739352 10062
## - EducationFieldMarketing           1    4709807 1246449159 10063
## - YearsInCurrentRole                1    6621697 1248361050 10064
## - YearsWithCurrManager              1    7589274 1249328626 10065
## - DistanceFromHome                  1   15243723 1256983076 10069
## - BusinessTravelTravel_Rarely       1   19035801 1260775153 10071
## - TotalWorkingYears                 1   59076058 1300815410 10093
## - JobLevel                          1 3996738185 5238477537 11062
## 
## Step:  AIC=10060.61
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     GenderMale + JobInvolvement + JobLevel + MaritalStatusMarried + 
##     MaritalStatusSingle + NumCompaniesWorked + OverTimeYes + 
##     RelationshipSatisfaction + TotalWorkingYears + WorkLifeBalance + 
##     YearsInCurrentRole + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - `EducationFieldTechnical Degree`  1     165475 1242068670 10059
## - WorkLifeBalance                   1     253449 1242156643 10059
## - OverTimeYes                       1     257427 1242160622 10059
## - NumCompaniesWorked                1     768633 1242671828 10059
## - `EducationFieldLife Sciences`     1    1099036 1243002231 10059
## - EducationFieldMedical             1    1139969 1243043164 10059
## - MaritalStatusSingle               1    1164352 1243067547 10059
## - RelationshipSatisfaction          1    1277529 1243180724 10059
## - JobInvolvement                    1    1358931 1243262126 10059
## - EducationFieldOther               1    1643417 1243546612 10060
## - MaritalStatusMarried              1    2358986 1244262181 10060
## - EnvironmentSatisfaction           1    2590942 1244494137 10060
## - GenderMale                        1    2768111 1244671306 10060
## <none>                                           1241903195 10061
## - EducationFieldMarketing           1    4651167 1246554362 10061
## - YearsInCurrentRole                1    6663026 1248566221 10062
## - YearsWithCurrManager              1    7483223 1249386418 10063
## - DistanceFromHome                  1   15127700 1257030895 10067
## - BusinessTravelTravel_Rarely       1   19232558 1261135753 10069
## - TotalWorkingYears                 1   59174258 1301077453 10091
## - JobLevel                          1 4003672900 5245576095 11061
## 
## Step:  AIC=10058.71
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     MaritalStatusMarried + MaritalStatusSingle + NumCompaniesWorked + 
##     OverTimeYes + RelationshipSatisfaction + TotalWorkingYears + 
##     WorkLifeBalance + YearsInCurrentRole + YearsWithCurrManager
## 
##                                 Df  Sum of Sq        RSS   AIC
## - WorkLifeBalance                1     252925 1242321595 10057
## - OverTimeYes                    1     254290 1242322961 10057
## - NumCompaniesWorked             1     808107 1242876777 10057
## - MaritalStatusSingle            1    1137876 1243206546 10057
## - RelationshipSatisfaction       1    1305500 1243374170 10057
## - JobInvolvement                 1    1358043 1243426713 10058
## - `EducationFieldLife Sciences`  1    2109770 1244178440 10058
## - EducationFieldMedical          1    2140131 1244208801 10058
## - EducationFieldOther            1    2315865 1244384535 10058
## - MaritalStatusMarried           1    2385147 1244453817 10058
## - EnvironmentSatisfaction        1    2605400 1244674071 10058
## - GenderMale                     1    2843769 1244912439 10058
## <none>                                        1242068670 10059
## - YearsInCurrentRole             1    6765060 1248833730 10060
## - YearsWithCurrManager           1    7453052 1249521722 10061
## - EducationFieldMarketing        1   10922965 1252991635 10063
## - DistanceFromHome               1   15106222 1257174892 10065
## - BusinessTravelTravel_Rarely    1   19236164 1261304834 10067
## - TotalWorkingYears              1   59307273 1301375943 10089
## - JobLevel                       1 4004609391 5246678061 11060
## 
## Step:  AIC=10056.85
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     MaritalStatusMarried + MaritalStatusSingle + NumCompaniesWorked + 
##     OverTimeYes + RelationshipSatisfaction + TotalWorkingYears + 
##     YearsInCurrentRole + YearsWithCurrManager
## 
##                                 Df  Sum of Sq        RSS   AIC
## - OverTimeYes                    1     243494 1242565089 10055
## - NumCompaniesWorked             1     829996 1243151591 10055
## - MaritalStatusSingle            1    1152973 1243474569 10056
## - RelationshipSatisfaction       1    1280749 1243602345 10056
## - JobInvolvement                 1    1344193 1243665788 10056
## - `EducationFieldLife Sciences`  1    2031562 1244353157 10056
## - EducationFieldMedical          1    2093079 1244414675 10056
## - EducationFieldOther            1    2283055 1244604651 10056
## - MaritalStatusMarried           1    2423493 1244745088 10056
## - EnvironmentSatisfaction        1    2698276 1245019872 10056
## - GenderMale                     1    2875816 1245197412 10056
## <none>                                        1242321595 10057
## - YearsInCurrentRole             1    7084500 1249406095 10059
## - YearsWithCurrManager           1    7364795 1249686391 10059
## - EducationFieldMarketing        1   10867706 1253189301 10061
## - DistanceFromHome               1   15093263 1257414858 10063
## - BusinessTravelTravel_Rarely    1   19349926 1261671521 10066
## - TotalWorkingYears              1   59516854 1301838449 10087
## - JobLevel                       1 4004869954 5247191549 11058
## 
## Step:  AIC=10054.99
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     MaritalStatusMarried + MaritalStatusSingle + NumCompaniesWorked + 
##     RelationshipSatisfaction + TotalWorkingYears + YearsInCurrentRole + 
##     YearsWithCurrManager
## 
##                                 Df  Sum of Sq        RSS   AIC
## - NumCompaniesWorked             1     825481 1243390570 10053
## - MaritalStatusSingle            1    1150241 1243715331 10054
## - RelationshipSatisfaction       1    1285159 1243850248 10054
## - JobInvolvement                 1    1317227 1243882316 10054
## - `EducationFieldLife Sciences`  1    2003279 1244568368 10054
## - EducationFieldMedical          1    2071929 1244637018 10054
## - EducationFieldOther            1    2249684 1244814773 10054
## - MaritalStatusMarried           1    2329585 1244894674 10054
## - EnvironmentSatisfaction        1    2624139 1245189228 10054
## - GenderMale                     1    2879945 1245445034 10055
## <none>                                        1242565089 10055
## - YearsInCurrentRole             1    7066004 1249631093 10057
## - YearsWithCurrManager           1    7501632 1250066721 10057
## - EducationFieldMarketing        1   10787928 1253353017 10059
## - DistanceFromHome               1   14939326 1257504415 10061
## - BusinessTravelTravel_Rarely    1   19416940 1261982029 10064
## - TotalWorkingYears              1   59591802 1302156891 10086
## - JobLevel                       1 4004792996 5247358085 11056
## 
## Step:  AIC=10053.45
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     MaritalStatusMarried + MaritalStatusSingle + RelationshipSatisfaction + 
##     TotalWorkingYears + YearsInCurrentRole + YearsWithCurrManager
## 
##                                 Df  Sum of Sq        RSS   AIC
## - MaritalStatusSingle            1    1078078 1244468648 10052
## - RelationshipSatisfaction       1    1209728 1244600299 10052
## - JobInvolvement                 1    1323647 1244714218 10052
## - `EducationFieldLife Sciences`  1    2011811 1245402381 10053
## - EducationFieldMedical          1    2056776 1245447347 10053
## - EducationFieldOther            1    2185800 1245576371 10053
## - MaritalStatusMarried           1    2200746 1245591316 10053
## - EnvironmentSatisfaction        1    2641019 1246031590 10053
## - GenderMale                     1    2891869 1246282439 10053
## <none>                                        1243390570 10053
## - YearsInCurrentRole             1    6705865 1250096435 10055
## - YearsWithCurrManager           1    6856765 1250247336 10055
## - EducationFieldMarketing        1   10662411 1254052982 10057
## - DistanceFromHome               1   14753206 1258143777 10060
## - BusinessTravelTravel_Rarely    1   19545535 1262936105 10062
## - TotalWorkingYears              1   62063027 1305453597 10085
## - JobLevel                       1 4091431804 5334822374 11065
## 
## Step:  AIC=10052.05
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     MaritalStatusMarried + RelationshipSatisfaction + TotalWorkingYears + 
##     YearsInCurrentRole + YearsWithCurrManager
## 
##                                 Df  Sum of Sq        RSS   AIC
## - MaritalStatusMarried           1    1154446 1245623095 10051
## - JobInvolvement                 1    1162935 1245631583 10051
## - RelationshipSatisfaction       1    1228516 1245697164 10051
## - `EducationFieldLife Sciences`  1    1959409 1246428057 10051
## - EducationFieldMedical          1    2053457 1246522106 10051
## - EducationFieldOther            1    2115401 1246584050 10051
## - GenderMale                     1    2629937 1247098585 10052
## - EnvironmentSatisfaction        1    2796794 1247265442 10052
## <none>                                        1244468648 10052
## - YearsWithCurrManager           1    6686087 1251154736 10054
## - YearsInCurrentRole             1    6964979 1251433627 10054
## - EducationFieldMarketing        1   10565755 1255034403 10056
## - DistanceFromHome               1   15203453 1259672102 10058
## - BusinessTravelTravel_Rarely    1   19386910 1263855559 10061
## - TotalWorkingYears              1   61686371 1306155019 10084
## - JobLevel                       1 4090613000 5335081648 11063
## 
## Step:  AIC=10050.7
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     RelationshipSatisfaction + TotalWorkingYears + YearsInCurrentRole + 
##     YearsWithCurrManager
## 
##                                 Df  Sum of Sq        RSS   AIC
## - RelationshipSatisfaction       1    1044823 1246667918 10049
## - JobInvolvement                 1    1302997 1246926091 10049
## - `EducationFieldLife Sciences`  1    2127547 1247750641 10050
## - EducationFieldOther            1    2226443 1247849538 10050
## - EducationFieldMedical          1    2250690 1247873784 10050
## - GenderMale                     1    2483938 1248107032 10050
## - EnvironmentSatisfaction        1    2822131 1248445226 10050
## <none>                                        1245623095 10051
## - YearsInCurrentRole             1    6677878 1252300972 10052
## - YearsWithCurrManager           1    6983716 1252606811 10053
## - EducationFieldMarketing        1   11210041 1256833136 10055
## - DistanceFromHome               1   14380388 1260003483 10057
## - BusinessTravelTravel_Rarely    1   20503620 1266126715 10060
## - TotalWorkingYears              1   62268452 1307891546 10083
## - JobLevel                       1 4091323805 5336946900 11061
## 
## Step:  AIC=10049.28
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     TotalWorkingYears + YearsInCurrentRole + YearsWithCurrManager
## 
##                                 Df  Sum of Sq        RSS   AIC
## - JobInvolvement                 1    1420377 1248088295 10048
## - EducationFieldMedical          1    2146101 1248814018 10048
## - `EducationFieldLife Sciences`  1    2198658 1248866576 10048
## - EducationFieldOther            1    2288614 1248956531 10049
## - GenderMale                     1    2491934 1249159852 10049
## - EnvironmentSatisfaction        1    2861066 1249528983 10049
## <none>                                        1246667918 10049
## - YearsInCurrentRole             1    6476274 1253144192 10051
## - YearsWithCurrManager           1    7161491 1253829409 10051
## - EducationFieldMarketing        1   11104598 1257772516 10054
## - DistanceFromHome               1   14305154 1260973071 10055
## - BusinessTravelTravel_Rarely    1   20043752 1266711670 10058
## - TotalWorkingYears              1   61907425 1308575343 10081
## - JobLevel                       1 4092892177 5339560095 11060
## 
## Step:  AIC=10048.07
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     EnvironmentSatisfaction + GenderMale + JobLevel + TotalWorkingYears + 
##     YearsInCurrentRole + YearsWithCurrManager
## 
##                                 Df  Sum of Sq        RSS   AIC
## - `EducationFieldLife Sciences`  1    2042625 1250130920 10047
## - EducationFieldMedical          1    2102621 1250190916 10047
## - EducationFieldOther            1    2331615 1250419910 10047
## - GenderMale                     1    2612842 1250701137 10048
## - EnvironmentSatisfaction        1    2797169 1250885463 10048
## <none>                                        1248088295 10048
## - YearsInCurrentRole             1    6511479 1254599774 10050
## - YearsWithCurrManager           1    7163334 1255251628 10050
## - EducationFieldMarketing        1   10962959 1259051253 10052
## - DistanceFromHome               1   14444666 1262532961 10054
## - BusinessTravelTravel_Rarely    1   20153978 1268242273 10057
## - TotalWorkingYears              1   62294305 1310382600 10080
## - JobLevel                       1 4091979906 5340068200 11058
## 
## Step:  AIC=10047.21
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + EnvironmentSatisfaction + 
##     GenderMale + JobLevel + TotalWorkingYears + YearsInCurrentRole + 
##     YearsWithCurrManager
## 
##                               Df  Sum of Sq        RSS   AIC
## - EducationFieldMedical        1     279819 1250410739 10045
## - EducationFieldOther          1     826362 1250957281 10046
## - EnvironmentSatisfaction      1    2602557 1252733476 10047
## - GenderMale                   1    2637803 1252768723 10047
## <none>                                      1250130920 10047
## - YearsInCurrentRole           1    6496973 1256627893 10049
## - YearsWithCurrManager         1    7297794 1257428713 10049
## - EducationFieldMarketing      1   10030769 1260161689 10051
## - DistanceFromHome             1   14369818 1264500738 10053
## - BusinessTravelTravel_Rarely  1   19888061 1270018981 10056
## - TotalWorkingYears            1   61950826 1312081746 10079
## - JobLevel                     1 4090693176 5340824096 11056
## 
## Step:  AIC=10045.37
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + EducationFieldMarketing + 
##     EducationFieldOther + EnvironmentSatisfaction + GenderMale + 
##     JobLevel + TotalWorkingYears + YearsInCurrentRole + YearsWithCurrManager
## 
##                               Df  Sum of Sq        RSS   AIC
## - EducationFieldOther          1     683169 1251093908 10044
## - EnvironmentSatisfaction      1    2559700 1252970439 10045
## - GenderMale                   1    2606972 1253017711 10045
## <none>                                      1250410739 10045
## - YearsInCurrentRole           1    6428895 1256839634 10047
## - YearsWithCurrManager         1    7308309 1257719048 10047
## - EducationFieldMarketing      1    9842701 1260253441 10049
## - DistanceFromHome             1   14541922 1264952661 10051
## - BusinessTravelTravel_Rarely  1   20112489 1270523228 10054
## - TotalWorkingYears            1   61676957 1312087696 10077
## - JobLevel                     1 4101505636 5351916375 11055
## 
## Step:  AIC=10043.75
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + EducationFieldMarketing + 
##     EnvironmentSatisfaction + GenderMale + JobLevel + TotalWorkingYears + 
##     YearsInCurrentRole + YearsWithCurrManager
## 
##                               Df  Sum of Sq        RSS   AIC
## - GenderMale                   1    2631542 1253725451 10043
## - EnvironmentSatisfaction      1    2686105 1253780013 10043
## <none>                                      1251093908 10044
## - YearsInCurrentRole           1    6373426 1257467334 10045
## - YearsWithCurrManager         1    7256529 1258350438 10046
## - EducationFieldMarketing      1    9452762 1260546671 10047
## - DistanceFromHome             1   14610985 1265704893 10050
## - BusinessTravelTravel_Rarely  1   19918303 1271012212 10053
## - TotalWorkingYears            1   62212912 1313306820 10076
## - JobLevel                     1 4107527916 5358621825 11054
## 
## Step:  AIC=10043.21
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + EducationFieldMarketing + 
##     EnvironmentSatisfaction + JobLevel + TotalWorkingYears + 
##     YearsInCurrentRole + YearsWithCurrManager
## 
##                               Df  Sum of Sq        RSS   AIC
## - EnvironmentSatisfaction      1    2504413 1256229864 10043
## <none>                                      1253725451 10043
## - YearsInCurrentRole           1    6716521 1260441972 10045
## - YearsWithCurrManager         1    7398623 1261124074 10045
## - EducationFieldMarketing      1    9886435 1263611886 10047
## - DistanceFromHome             1   14729080 1268454531 10049
## - BusinessTravelTravel_Rarely  1   19998417 1273723867 10052
## - TotalWorkingYears            1   62612638 1316338089 10075
## - JobLevel                     1 4105030353 5358755804 11052
## 
## Step:  AIC=10042.6
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + EducationFieldMarketing + 
##     JobLevel + TotalWorkingYears + YearsInCurrentRole + YearsWithCurrManager
## 
##                               Df  Sum of Sq        RSS   AIC
## <none>                                      1256229864 10043
## - YearsWithCurrManager         1    7045659 1263275523 10044
## - YearsInCurrentRole           1    7403285 1263633150 10045
## - EducationFieldMarketing      1    9516341 1265746205 10046
## - DistanceFromHome             1   14095845 1270325709 10048
## - BusinessTravelTravel_Rarely  1   20210484 1276440348 10052
## - TotalWorkingYears            1   64498823 1320728687 10075
## - JobLevel                     1 4106785341 5363015205 11051
## Start:  AIC=12595.08
## .outcome ~ Age + BusinessTravelTravel_Frequently + BusinessTravelTravel_Rarely + 
##     DistanceFromHome + Education + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     GenderMale + JobInvolvement + JobLevel + JobSatisfaction + 
##     MaritalStatusMarried + MaritalStatusSingle + NumCompaniesWorked + 
##     OverTimeYes + PercentSalaryHike + RelationshipSatisfaction + 
##     StockOptionLevel + TotalWorkingYears + TrainingTimesLastYear + 
##     WorkLifeBalance + YearsInCurrentRole + YearsSinceLastPromotion + 
##     YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - MaritalStatusSingle               1       2542 1577218827 12593
## - BusinessTravelTravel_Frequently   1       7882 1577224167 12593
## - JobSatisfaction                   1      17251 1577233536 12593
## - StockOptionLevel                  1      31224 1577247509 12593
## - YearsSinceLastPromotion           1      69313 1577285598 12593
## - OverTimeYes                       1     114616 1577330900 12593
## - Age                               1     169090 1577385375 12593
## - Education                         1     212930 1577429215 12593
## - NumCompaniesWorked                1     306436 1577522721 12593
## - WorkLifeBalance                   1     316761 1577533045 12593
## - TrainingTimesLastYear             1     567752 1577784036 12593
## - RelationshipSatisfaction          1     585972 1577802256 12593
## - EducationFieldMedical             1     964765 1578181049 12594
## - PercentSalaryHike                 1    1004558 1578220842 12594
## - `EducationFieldTechnical Degree`  1    1101873 1578318158 12594
## - GenderMale                        1    1176487 1578392771 12594
## - `EducationFieldLife Sciences`     1    1270965 1578487249 12594
## - YearsInCurrentRole                1    1310945 1578527230 12594
## - EducationFieldOther               1    1597998 1578814283 12594
## - MaritalStatusMarried              1    2601842 1579818127 12594
## <none>                                           1577216285 12595
## - JobInvolvement                    1    3794159 1581010444 12595
## - EnvironmentSatisfaction           1    4235882 1581452167 12595
## - EducationFieldMarketing           1    5605199 1582821483 12596
## - BusinessTravelTravel_Rarely       1    8098247 1585314532 12598
## - YearsWithCurrManager              1   12452252 1589668537 12600
## - DistanceFromHome                  1   13470922 1590687207 12600
## - TotalWorkingYears                 1   50175612 1627391897 12620
## - JobLevel                          1 5411462666 6988678951 13888
## 
## Step:  AIC=12593.08
## .outcome ~ Age + BusinessTravelTravel_Frequently + BusinessTravelTravel_Rarely + 
##     DistanceFromHome + Education + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     GenderMale + JobInvolvement + JobLevel + JobSatisfaction + 
##     MaritalStatusMarried + NumCompaniesWorked + OverTimeYes + 
##     PercentSalaryHike + RelationshipSatisfaction + StockOptionLevel + 
##     TotalWorkingYears + TrainingTimesLastYear + WorkLifeBalance + 
##     YearsInCurrentRole + YearsSinceLastPromotion + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - BusinessTravelTravel_Frequently   1       7179 1577226006 12591
## - JobSatisfaction                   1      17974 1577236800 12591
## - StockOptionLevel                  1      35300 1577254127 12591
## - YearsSinceLastPromotion           1      70003 1577288830 12591
## - OverTimeYes                       1     114744 1577333571 12591
## - Age                               1     166920 1577385746 12591
## - Education                         1     212045 1577430871 12591
## - NumCompaniesWorked                1     310283 1577529110 12591
## - WorkLifeBalance                   1     317930 1577536756 12591
## - TrainingTimesLastYear             1     566490 1577785317 12591
## - RelationshipSatisfaction          1     585192 1577804019 12591
## - EducationFieldMedical             1     968082 1578186909 12592
## - PercentSalaryHike                 1    1004488 1578223315 12592
## - `EducationFieldTechnical Degree`  1    1105209 1578324036 12592
## - GenderMale                        1    1191335 1578410162 12592
## - `EducationFieldLife Sciences`     1    1276332 1578495158 12592
## - YearsInCurrentRole                1    1309766 1578528593 12592
## - EducationFieldOther               1    1599317 1578818144 12592
## <none>                                           1577218827 12593
## - JobInvolvement                    1    3806857 1581025683 12593
## - EnvironmentSatisfaction           1    4234410 1581453237 12593
## - MaritalStatusMarried              1    4689173 1581907999 12594
## - EducationFieldMarketing           1    5621191 1582840018 12594
## - BusinessTravelTravel_Rarely       1    8109783 1585328609 12596
## - YearsWithCurrManager              1   12502066 1589720892 12598
## - DistanceFromHome                  1   13485959 1590704785 12598
## - TotalWorkingYears                 1   50174600 1627393426 12618
## - JobLevel                          1 5419030911 6996249738 13887
## 
## Step:  AIC=12591.08
## .outcome ~ Age + BusinessTravelTravel_Rarely + DistanceFromHome + 
##     Education + `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     JobSatisfaction + MaritalStatusMarried + NumCompaniesWorked + 
##     OverTimeYes + PercentSalaryHike + RelationshipSatisfaction + 
##     StockOptionLevel + TotalWorkingYears + TrainingTimesLastYear + 
##     WorkLifeBalance + YearsInCurrentRole + YearsSinceLastPromotion + 
##     YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - JobSatisfaction                   1      16786 1577242792 12589
## - StockOptionLevel                  1      36450 1577262455 12589
## - YearsSinceLastPromotion           1      70567 1577296573 12589
## - OverTimeYes                       1     118622 1577344628 12589
## - Age                               1     169981 1577395987 12589
## - Education                         1     211972 1577437978 12589
## - NumCompaniesWorked                1     313920 1577539926 12589
## - WorkLifeBalance                   1     318036 1577544041 12589
## - TrainingTimesLastYear             1     576029 1577802035 12589
## - RelationshipSatisfaction          1     591542 1577817547 12589
## - EducationFieldMedical             1     969215 1578195221 12590
## - PercentSalaryHike                 1    1001877 1578227882 12590
## - `EducationFieldTechnical Degree`  1    1104607 1578330613 12590
## - GenderMale                        1    1185270 1578411275 12590
## - `EducationFieldLife Sciences`     1    1275440 1578501446 12590
## - YearsInCurrentRole                1    1309311 1578535317 12590
## - EducationFieldOther               1    1598641 1578824647 12590
## <none>                                           1577226006 12591
## - JobInvolvement                    1    3839102 1581065108 12591
## - EnvironmentSatisfaction           1    4230977 1581456982 12591
## - MaritalStatusMarried              1    4694164 1581920170 12592
## - EducationFieldMarketing           1    5617701 1582843707 12592
## - YearsWithCurrManager              1   12523366 1589749372 12596
## - DistanceFromHome                  1   13511107 1590737112 12596
## - BusinessTravelTravel_Rarely       1   16932372 1594158378 12598
## - TotalWorkingYears                 1   50474575 1627700580 12616
## - JobLevel                          1 5424927818 7002153824 13886
## 
## Step:  AIC=12589.09
## .outcome ~ Age + BusinessTravelTravel_Rarely + DistanceFromHome + 
##     Education + `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     MaritalStatusMarried + NumCompaniesWorked + OverTimeYes + 
##     PercentSalaryHike + RelationshipSatisfaction + StockOptionLevel + 
##     TotalWorkingYears + TrainingTimesLastYear + WorkLifeBalance + 
##     YearsInCurrentRole + YearsSinceLastPromotion + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - StockOptionLevel                  1      36521 1577279313 12587
## - YearsSinceLastPromotion           1      72774 1577315566 12587
## - OverTimeYes                       1     116187 1577358978 12587
## - Age                               1     172165 1577414956 12587
## - Education                         1     208567 1577451358 12587
## - NumCompaniesWorked                1     305159 1577547951 12587
## - WorkLifeBalance                   1     315373 1577558165 12587
## - TrainingTimesLastYear             1     584341 1577827133 12587
## - RelationshipSatisfaction          1     597981 1577840773 12587
## - EducationFieldMedical             1     977880 1578220672 12588
## - PercentSalaryHike                 1    1001027 1578243818 12588
## - `EducationFieldTechnical Degree`  1    1109187 1578351979 12588
## - GenderMale                        1    1179761 1578422553 12588
## - `EducationFieldLife Sciences`     1    1286155 1578528947 12588
## - YearsInCurrentRole                1    1313270 1578556061 12588
## - EducationFieldOther               1    1609127 1578851919 12588
## <none>                                           1577242792 12589
## - JobInvolvement                    1    3879755 1581122547 12589
## - EnvironmentSatisfaction           1    4222897 1581465689 12589
## - MaritalStatusMarried              1    4697622 1581940414 12590
## - EducationFieldMarketing           1    5635753 1582878545 12590
## - YearsWithCurrManager              1   12536847 1589779639 12594
## - DistanceFromHome                  1   13494396 1590737187 12594
## - BusinessTravelTravel_Rarely       1   17096970 1594339761 12596
## - TotalWorkingYears                 1   50498141 1627740933 12614
## - JobLevel                          1 5428324564 7005567356 13884
## 
## Step:  AIC=12587.11
## .outcome ~ Age + BusinessTravelTravel_Rarely + DistanceFromHome + 
##     Education + `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     MaritalStatusMarried + NumCompaniesWorked + OverTimeYes + 
##     PercentSalaryHike + RelationshipSatisfaction + TotalWorkingYears + 
##     TrainingTimesLastYear + WorkLifeBalance + YearsInCurrentRole + 
##     YearsSinceLastPromotion + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - YearsSinceLastPromotion           1      76314 1577355627 12585
## - OverTimeYes                       1     115742 1577395055 12585
## - Age                               1     174654 1577453967 12585
## - Education                         1     205816 1577485129 12585
## - NumCompaniesWorked                1     313450 1577592763 12585
## - WorkLifeBalance                   1     325473 1577604786 12585
## - TrainingTimesLastYear             1     575363 1577854676 12585
## - RelationshipSatisfaction          1     607562 1577886875 12585
## - EducationFieldMedical             1     975732 1578255045 12586
## - PercentSalaryHike                 1    1004337 1578283650 12586
## - `EducationFieldTechnical Degree`  1    1103240 1578382553 12586
## - GenderMale                        1    1164543 1578443856 12586
## - `EducationFieldLife Sciences`     1    1282564 1578561877 12586
## - YearsInCurrentRole                1    1350155 1578629468 12586
## - EducationFieldOther               1    1591200 1578870513 12586
## <none>                                           1577279313 12587
## - JobInvolvement                    1    3847055 1581126368 12587
## - EnvironmentSatisfaction           1    4259481 1581538794 12588
## - MaritalStatusMarried              1    4710454 1581989767 12588
## - EducationFieldMarketing           1    5632462 1582911775 12588
## - YearsWithCurrManager              1   12530708 1589810021 12592
## - DistanceFromHome                  1   13622265 1590901578 12593
## - BusinessTravelTravel_Rarely       1   17121409 1594400723 12594
## - TotalWorkingYears                 1   50519515 1627798828 12612
## - JobLevel                          1 5430181541 7007460854 13882
## 
## Step:  AIC=12585.15
## .outcome ~ Age + BusinessTravelTravel_Rarely + DistanceFromHome + 
##     Education + `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     MaritalStatusMarried + NumCompaniesWorked + OverTimeYes + 
##     PercentSalaryHike + RelationshipSatisfaction + TotalWorkingYears + 
##     TrainingTimesLastYear + WorkLifeBalance + YearsInCurrentRole + 
##     YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - OverTimeYes                       1     117781 1577473408 12583
## - Age                               1     180906 1577536533 12583
## - Education                         1     213677 1577569304 12583
## - WorkLifeBalance                   1     321496 1577677123 12583
## - NumCompaniesWorked                1     352429 1577708056 12583
## - TrainingTimesLastYear             1     555442 1577911069 12584
## - RelationshipSatisfaction          1     631247 1577986874 12584
## - EducationFieldMedical             1     948173 1578303799 12584
## - PercentSalaryHike                 1     977952 1578333579 12584
## - `EducationFieldTechnical Degree`  1    1073760 1578429387 12584
## - GenderMale                        1    1182117 1578537744 12584
## - `EducationFieldLife Sciences`     1    1256324 1578611951 12584
## - YearsInCurrentRole                1    1274067 1578629694 12584
## - EducationFieldOther               1    1576549 1578932176 12584
## <none>                                           1577355627 12585
## - JobInvolvement                    1    3806471 1581162098 12585
## - EnvironmentSatisfaction           1    4240970 1581596597 12586
## - MaritalStatusMarried              1    4743419 1582099046 12586
## - EducationFieldMarketing           1    5585982 1582941609 12586
## - YearsWithCurrManager              1   12499255 1589854882 12590
## - DistanceFromHome                  1   13692598 1591048225 12591
## - BusinessTravelTravel_Rarely       1   17047113 1594402740 12592
## - TotalWorkingYears                 1   53159167 1630514794 12612
## - JobLevel                          1 5444409116 7021764743 13882
## 
## Step:  AIC=12583.22
## .outcome ~ Age + BusinessTravelTravel_Rarely + DistanceFromHome + 
##     Education + `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     MaritalStatusMarried + NumCompaniesWorked + PercentSalaryHike + 
##     RelationshipSatisfaction + TotalWorkingYears + TrainingTimesLastYear + 
##     WorkLifeBalance + YearsInCurrentRole + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - Age                               1     171934 1577645342 12581
## - Education                         1     205672 1577679079 12581
## - WorkLifeBalance                   1     322833 1577796241 12581
## - NumCompaniesWorked                1     353060 1577826468 12581
## - TrainingTimesLastYear             1     527995 1578001402 12582
## - RelationshipSatisfaction          1     639472 1578112880 12582
## - EducationFieldMedical             1     930016 1578403424 12582
## - PercentSalaryHike                 1     970016 1578443424 12582
## - `EducationFieldTechnical Degree`  1    1067021 1578540429 12582
## - GenderMale                        1    1179303 1578652710 12582
## - `EducationFieldLife Sciences`     1    1238812 1578712219 12582
## - YearsInCurrentRole                1    1268500 1578741908 12582
## - EducationFieldOther               1    1549178 1579022586 12582
## <none>                                           1577473408 12583
## - JobInvolvement                    1    3762149 1581235557 12583
## - EnvironmentSatisfaction           1    4170637 1581644045 12584
## - MaritalStatusMarried              1    4682762 1582156170 12584
## - EducationFieldMarketing           1    5553928 1583027336 12584
## - YearsWithCurrManager              1   12566857 1590040264 12588
## - DistanceFromHome                  1   13585299 1591058707 12589
## - BusinessTravelTravel_Rarely       1   17109906 1594583313 12591
## - TotalWorkingYears                 1   53082322 1630555730 12610
## - JobLevel                          1 5444302193 7021775601 13880
## 
## Step:  AIC=12581.31
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + Education + 
##     `EducationFieldLife Sciences` + EducationFieldMarketing + 
##     EducationFieldMedical + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     MaritalStatusMarried + NumCompaniesWorked + PercentSalaryHike + 
##     RelationshipSatisfaction + TotalWorkingYears + TrainingTimesLastYear + 
##     WorkLifeBalance + YearsInCurrentRole + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - Education                         1     156249 1577801591 12579
## - WorkLifeBalance                   1     309901 1577955243 12580
## - NumCompaniesWorked                1     402764 1578048106 12580
## - TrainingTimesLastYear             1     526771 1578172113 12580
## - RelationshipSatisfaction          1     630086 1578275427 12580
## - EducationFieldMedical             1     937803 1578583145 12580
## - PercentSalaryHike                 1     956298 1578601640 12580
## - `EducationFieldTechnical Degree`  1    1081753 1578727095 12580
## - GenderMale                        1    1185426 1578830768 12580
## - YearsInCurrentRole                1    1216690 1578862032 12580
## - `EducationFieldLife Sciences`     1    1264820 1578910161 12580
## - EducationFieldOther               1    1539271 1579184613 12580
## <none>                                           1577645342 12581
## - JobInvolvement                    1    3716358 1581361700 12581
## - EnvironmentSatisfaction           1    4208730 1581854071 12582
## - MaritalStatusMarried              1    4604570 1582249911 12582
## - EducationFieldMarketing           1    5624383 1583269725 12582
## - YearsWithCurrManager              1   12409932 1590055274 12586
## - DistanceFromHome                  1   13570855 1591216197 12587
## - BusinessTravelTravel_Rarely       1   17219399 1594864741 12589
## - TotalWorkingYears                 1   66198757 1643844099 12615
## - JobLevel                          1 5469023869 7046669210 13881
## 
## Step:  AIC=12579.4
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     GenderMale + JobInvolvement + JobLevel + MaritalStatusMarried + 
##     NumCompaniesWorked + PercentSalaryHike + RelationshipSatisfaction + 
##     TotalWorkingYears + TrainingTimesLastYear + WorkLifeBalance + 
##     YearsInCurrentRole + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - WorkLifeBalance                   1     306637 1578108228 12578
## - NumCompaniesWorked                1     339578 1578141169 12578
## - TrainingTimesLastYear             1     504323 1578305914 12578
## - RelationshipSatisfaction          1     611995 1578413585 12578
## - EducationFieldMedical             1     952759 1578754350 12578
## - PercentSalaryHike                 1     964440 1578766030 12578
## - `EducationFieldTechnical Degree`  1    1088644 1578890234 12578
## - GenderMale                        1    1192344 1578993935 12578
## - YearsInCurrentRole                1    1231047 1579032638 12578
## - `EducationFieldLife Sciences`     1    1274199 1579075790 12578
## - EducationFieldOther               1    1541161 1579342751 12578
## <none>                                           1577801591 12579
## - JobInvolvement                    1    3777533 1581579124 12580
## - EnvironmentSatisfaction           1    4274574 1582076165 12580
## - MaritalStatusMarried              1    4602919 1582404509 12580
## - EducationFieldMarketing           1    5582503 1583384093 12580
## - YearsWithCurrManager              1   12282079 1590083670 12584
## - DistanceFromHome                  1   13450752 1591252342 12585
## - BusinessTravelTravel_Rarely       1   17287022 1595088612 12587
## - TotalWorkingYears                 1   66377686 1644179277 12613
## - JobLevel                          1 5474434667 7052236257 13880
## 
## Step:  AIC=12577.57
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     GenderMale + JobInvolvement + JobLevel + MaritalStatusMarried + 
##     NumCompaniesWorked + PercentSalaryHike + RelationshipSatisfaction + 
##     TotalWorkingYears + TrainingTimesLastYear + YearsInCurrentRole + 
##     YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - NumCompaniesWorked                1     362509 1578470737 12576
## - TrainingTimesLastYear             1     484361 1578592589 12576
## - RelationshipSatisfaction          1     586442 1578694670 12576
## - EducationFieldMedical             1     917607 1579025835 12576
## - PercentSalaryHike                 1     950169 1579058397 12576
## - `EducationFieldTechnical Degree`  1    1073341 1579181569 12576
## - GenderMale                        1    1217853 1579326080 12576
## - `EducationFieldLife Sciences`     1    1231328 1579339556 12576
## - YearsInCurrentRole                1    1365197 1579473424 12576
## - EducationFieldOther               1    1517542 1579625770 12576
## <none>                                           1578108228 12578
## - JobInvolvement                    1    3756267 1581864495 12578
## - EnvironmentSatisfaction           1    4469907 1582578135 12578
## - MaritalStatusMarried              1    4641594 1582749822 12578
## - EducationFieldMarketing           1    5530210 1583638438 12579
## - YearsWithCurrManager              1   12131699 1590239926 12582
## - DistanceFromHome                  1   13408764 1591516992 12583
## - BusinessTravelTravel_Rarely       1   17433569 1595541797 12585
## - TotalWorkingYears                 1   66684365 1644792593 12612
## - JobLevel                          1 5475251523 7053359751 13878
## 
## Step:  AIC=12575.77
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     GenderMale + JobInvolvement + JobLevel + MaritalStatusMarried + 
##     PercentSalaryHike + RelationshipSatisfaction + TotalWorkingYears + 
##     TrainingTimesLastYear + YearsInCurrentRole + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - RelationshipSatisfaction          1     537019 1579007755 12574
## - TrainingTimesLastYear             1     541708 1579012445 12574
## - PercentSalaryHike                 1     953756 1579424493 12574
## - EducationFieldMedical             1     955969 1579426705 12574
## - `EducationFieldTechnical Degree`  1    1127643 1579598379 12574
## - YearsInCurrentRole                1    1207781 1579678518 12574
## - GenderMale                        1    1241144 1579711881 12574
## - `EducationFieldLife Sciences`     1    1283584 1579754321 12574
## - EducationFieldOther               1    1549769 1580020506 12575
## <none>                                           1578470737 12576
## - JobInvolvement                    1    3753160 1582223896 12576
## - EnvironmentSatisfaction           1    4532105 1583002842 12576
## - MaritalStatusMarried              1    4611853 1583082590 12576
## - EducationFieldMarketing           1    5604690 1584075427 12577
## - YearsWithCurrManager              1   11775932 1590246669 12580
## - DistanceFromHome                  1   13188326 1591659063 12581
## - BusinessTravelTravel_Rarely       1   17568226 1596038963 12583
## - TotalWorkingYears                 1   71303025 1649773762 12612
## - JobLevel                          1 5536202742 7114673478 13884
## 
## Step:  AIC=12574.07
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     GenderMale + JobInvolvement + JobLevel + MaritalStatusMarried + 
##     PercentSalaryHike + TotalWorkingYears + TrainingTimesLastYear + 
##     YearsInCurrentRole + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - TrainingTimesLastYear             1     549366 1579557121 12572
## - PercentSalaryHike                 1     882972 1579890727 12573
## - EducationFieldMedical             1     991741 1579999496 12573
## - YearsInCurrentRole                1    1161726 1580169481 12573
## - `EducationFieldTechnical Degree`  1    1195856 1580203611 12573
## - GenderMale                        1    1257430 1580265186 12573
## - `EducationFieldLife Sciences`     1    1365695 1580373451 12573
## - EducationFieldOther               1    1632925 1580640680 12573
## <none>                                           1579007755 12574
## - JobInvolvement                    1    3813201 1582820957 12574
## - MaritalStatusMarried              1    4410557 1583418312 12574
## - EnvironmentSatisfaction           1    4522968 1583530724 12575
## - EducationFieldMarketing           1    5730670 1584738426 12575
## - YearsWithCurrManager              1   11924264 1590932019 12579
## - DistanceFromHome                  1   12999645 1592007400 12579
## - BusinessTravelTravel_Rarely       1   17272555 1596280311 12582
## - TotalWorkingYears                 1   71048878 1650056633 12610
## - JobLevel                          1 5539413461 7118421217 13882
## 
## Step:  AIC=12572.37
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     GenderMale + JobInvolvement + JobLevel + MaritalStatusMarried + 
##     PercentSalaryHike + TotalWorkingYears + YearsInCurrentRole + 
##     YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - PercentSalaryHike                 1     882152 1580439273 12571
## - EducationFieldMedical             1     934045 1580491166 12571
## - `EducationFieldTechnical Degree`  1    1158127 1580715248 12571
## - YearsInCurrentRole                1    1177912 1580735033 12571
## - GenderMale                        1    1255700 1580812821 12571
## - `EducationFieldLife Sciences`     1    1354636 1580911757 12571
## - EducationFieldOther               1    1600986 1581158107 12571
## <none>                                           1579557121 12572
## - JobInvolvement                    1    3751432 1583308553 12572
## - MaritalStatusMarried              1    4307977 1583865098 12573
## - EnvironmentSatisfaction           1    4573711 1584130832 12573
## - EducationFieldMarketing           1    5693853 1585250974 12574
## - YearsWithCurrManager              1   11804023 1591361144 12577
## - DistanceFromHome                  1   13231823 1592788944 12578
## - BusinessTravelTravel_Rarely       1   17223332 1596780453 12580
## - TotalWorkingYears                 1   70803058 1650360179 12608
## - JobLevel                          1 5538869866 7118426987 13880
## 
## Step:  AIC=12570.85
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldMedical + EducationFieldOther + 
##     `EducationFieldTechnical Degree` + EnvironmentSatisfaction + 
##     GenderMale + JobInvolvement + JobLevel + MaritalStatusMarried + 
##     TotalWorkingYears + YearsInCurrentRole + YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - EducationFieldMedical             1     900312 1581339585 12569
## - YearsInCurrentRole                1    1115860 1581555133 12570
## - `EducationFieldTechnical Degree`  1    1198622 1581637895 12570
## - GenderMale                        1    1245700 1581684973 12570
## - `EducationFieldLife Sciences`     1    1319594 1581758867 12570
## - EducationFieldOther               1    1544021 1581983293 12570
## <none>                                           1580439273 12571
## - JobInvolvement                    1    3800221 1584239494 12571
## - MaritalStatusMarried              1    4238887 1584678160 12571
## - EnvironmentSatisfaction           1    4567417 1585006690 12571
## - EducationFieldMarketing           1    5710644 1586149917 12572
## - YearsWithCurrManager              1   12026283 1592465556 12575
## - DistanceFromHome                  1   12897279 1593336552 12576
## - BusinessTravelTravel_Rarely       1   17228862 1597668134 12578
## - TotalWorkingYears                 1   70420406 1650859678 12607
## - JobLevel                          1 5538045947 7118485220 13878
## 
## Step:  AIC=12569.35
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldOther + `EducationFieldTechnical Degree` + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     MaritalStatusMarried + TotalWorkingYears + YearsInCurrentRole + 
##     YearsWithCurrManager
## 
##                                    Df  Sum of Sq        RSS   AIC
## - `EducationFieldTechnical Degree`  1     300928 1581640513 12568
## - `EducationFieldLife Sciences`     1     656765 1581996350 12568
## - EducationFieldOther               1     694093 1582033677 12568
## - YearsInCurrentRole                1    1150398 1582489983 12568
## - GenderMale                        1    1329476 1582669060 12568
## <none>                                           1581339585 12569
## - JobInvolvement                    1    3802576 1585142161 12569
## - EnvironmentSatisfaction           1    4494727 1585834311 12570
## - MaritalStatusMarried              1    4567351 1585906936 12570
## - YearsWithCurrManager              1   12147640 1593487225 12574
## - DistanceFromHome                  1   13038265 1594377849 12574
## - EducationFieldMarketing           1   13136738 1594476322 12574
## - BusinessTravelTravel_Rarely       1   17128297 1598467881 12577
## - TotalWorkingYears                 1   70342855 1651682439 12605
## - JobLevel                          1 5544158375 7125497959 13877
## 
## Step:  AIC=12567.51
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + `EducationFieldLife Sciences` + 
##     EducationFieldMarketing + EducationFieldOther + EnvironmentSatisfaction + 
##     GenderMale + JobInvolvement + JobLevel + MaritalStatusMarried + 
##     TotalWorkingYears + YearsInCurrentRole + YearsWithCurrManager
## 
##                                 Df  Sum of Sq        RSS   AIC
## - `EducationFieldLife Sciences`  1     438722 1582079234 12566
## - EducationFieldOther            1     554854 1582195367 12566
## - YearsInCurrentRole             1    1198420 1582838933 12566
## - GenderMale                     1    1348099 1582988611 12566
## <none>                                        1581640513 12568
## - JobInvolvement                 1    3812536 1585453049 12568
## - MaritalStatusMarried           1    4488235 1586128748 12568
## - EnvironmentSatisfaction        1    4587122 1586227635 12568
## - YearsWithCurrManager           1   12075731 1593716244 12572
## - DistanceFromHome               1   12896797 1594537309 12573
## - EducationFieldMarketing        1   12922393 1594562906 12573
## - BusinessTravelTravel_Rarely    1   17082647 1598723160 12575
## - TotalWorkingYears              1   71046809 1652687322 12604
## - JobLevel                       1 5543880292 7125520804 13875
## 
## Step:  AIC=12565.76
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + EducationFieldMarketing + 
##     EducationFieldOther + EnvironmentSatisfaction + GenderMale + 
##     JobInvolvement + JobLevel + MaritalStatusMarried + TotalWorkingYears + 
##     YearsInCurrentRole + YearsWithCurrManager
## 
##                               Df  Sum of Sq        RSS   AIC
## - EducationFieldOther          1     357353 1582436587 12564
## - YearsInCurrentRole           1    1190021 1583269255 12564
## - GenderMale                   1    1338565 1583417800 12564
## <none>                                      1582079234 12566
## - JobInvolvement               1    3772165 1585851399 12566
## - MaritalStatusMarried         1    4511766 1586591000 12566
## - EnvironmentSatisfaction      1    4556300 1586635534 12566
## - YearsWithCurrManager         1   12097238 1594176473 12570
## - DistanceFromHome             1   12755714 1594834948 12571
## - EducationFieldMarketing      1   12790926 1594870161 12571
## - BusinessTravelTravel_Rarely  1   16922277 1599001512 12573
## - TotalWorkingYears            1   71541114 1653620349 12602
## - JobLevel                     1 5548027776 7130107011 13874
## 
## Step:  AIC=12563.95
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + EducationFieldMarketing + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     MaritalStatusMarried + TotalWorkingYears + YearsInCurrentRole + 
##     YearsWithCurrManager
## 
##                               Df  Sum of Sq        RSS   AIC
## - YearsInCurrentRole           1    1185652 1583622239 12563
## - GenderMale                   1    1306186 1583742774 12563
## <none>                                      1582436587 12564
## - JobInvolvement               1    3878198 1586314785 12564
## - MaritalStatusMarried         1    4525054 1586961641 12564
## - EnvironmentSatisfaction      1    4749834 1587186421 12565
## - YearsWithCurrManager         1   12083079 1594519666 12569
## - EducationFieldMarketing      1   12500834 1594937422 12569
## - DistanceFromHome             1   12819835 1595256423 12569
## - BusinessTravelTravel_Rarely  1   16845050 1599281637 12571
## - TotalWorkingYears            1   72132444 1654569032 12601
## - JobLevel                     1 5551384114 7133820701 13872
## 
## Step:  AIC=12562.6
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + EducationFieldMarketing + 
##     EnvironmentSatisfaction + GenderMale + JobInvolvement + JobLevel + 
##     MaritalStatusMarried + TotalWorkingYears + YearsWithCurrManager
## 
##                               Df  Sum of Sq        RSS   AIC
## - GenderMale                   1    1393607 1585015846 12561
## <none>                                      1583622239 12563
## - JobInvolvement               1    3824983 1587447223 12563
## - MaritalStatusMarried         1    4172584 1587794824 12563
## - EnvironmentSatisfaction      1    5073964 1588696204 12563
## - EducationFieldMarketing      1   12554652 1596176892 12568
## - DistanceFromHome             1   12794396 1596416635 12568
## - BusinessTravelTravel_Rarely  1   16592918 1600215158 12570
## - YearsWithCurrManager         1   28480066 1612102306 12576
## - TotalWorkingYears            1   71021240 1654643480 12599
## - JobLevel                     1 5550206003 7133828242 13870
## 
## Step:  AIC=12561.37
## .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + EducationFieldMarketing + 
##     EnvironmentSatisfaction + JobInvolvement + JobLevel + MaritalStatusMarried + 
##     TotalWorkingYears + YearsWithCurrManager
## 
##                               Df  Sum of Sq        RSS   AIC
## <none>                                      1585015846 12561
## - JobInvolvement               1    3932348 1588948194 12562
## - MaritalStatusMarried         1    3970026 1588985873 12562
## - EnvironmentSatisfaction      1    4979154 1589995000 12562
## - DistanceFromHome             1   12838996 1597854842 12566
## - EducationFieldMarketing      1   12878775 1597894622 12566
## - BusinessTravelTravel_Rarely  1   16737293 1601753139 12568
## - YearsWithCurrManager         1   29408237 1614424083 12575
## - TotalWorkingYears            1   70832125 1655847971 12597
## - JobLevel                     1 5548821138 7133836984 13868
```

```r
# Final model
summary(emp_salary.step)
```

```
## 
## Call:
## lm(formula = .outcome ~ BusinessTravelTravel_Rarely + DistanceFromHome + 
##     EducationFieldMarketing + EnvironmentSatisfaction + JobInvolvement + 
##     JobLevel + MaritalStatusMarried + TotalWorkingYears + YearsWithCurrManager, 
##     data = dat)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -5857.8  -761.3     0.4   735.0  4244.8 
## 
## Coefficients:
##                              Estimate Std. Error t value Pr(>|t|)    
## (Intercept)                 -1887.027    253.357  -7.448 2.30e-13 ***
## BusinessTravelTravel_Rarely   308.194    102.270   3.014  0.00266 ** 
## DistanceFromHome              -15.086      5.716  -2.639  0.00846 ** 
## EducationFieldMarketing      -385.885    145.978  -2.643  0.00836 ** 
## EnvironmentSatisfaction       -68.977     41.965  -1.644  0.10061    
## JobInvolvement                 95.713     65.526   1.461  0.14447    
## JobLevel                     3745.203     68.256  54.870  < 2e-16 ***
## MaritalStatusMarried          137.169     93.461   1.468  0.14256    
## TotalWorkingYears              64.114     10.342   6.199 8.79e-10 ***
## YearsWithCurrManager          -58.150     14.557  -3.995 7.04e-05 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 1358 on 860 degrees of freedom
## Multiple R-squared:  0.9137,	Adjusted R-squared:  0.9128 
## F-statistic:  1012 on 9 and 860 DF,  p-value: < 2.2e-16
```

```r
# Results including RMSE of final model
emp_salary.step$results
```

```
##   parameter    RMSE  Rsquared      MAE   RMSESD  RsquaredSD    MAESD
## 1      none 1379.66 0.9101195 1034.596 54.37536 0.005402434 44.15991
```

### Comparison to Random Forest

The Random Forest performs slightly better with an Adjusted R-squared of 0.9465 and the RMSE of $1066.143



```r
# Compare to Randome Forest
# emp_salary_reg.df[,-c(4,12)] -> emp_salary_reg.df

train(MonthlyIncome ~ JobLevel + JobRole + TotalWorkingYears + YearsAtCompany + Age,
  data = emp_clean.df,
  method = "lm",
  trControl = train.cv
) -> emp_salary_rf.step

# Final model
summary(emp_salary_rf.step)
```

```
## 
## Call:
## lm(formula = .outcome ~ ., data = dat)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -3947.4  -637.6   -21.3   604.0  4122.0 
## 
## Coefficients:
##                                 Estimate Std. Error t value Pr(>|t|)    
## (Intercept)                      -67.470    263.485  -0.256  0.79796    
## JobLevel                        2800.149     82.407  33.979  < 2e-16 ***
## `JobRoleHuman Resources`        -317.625    252.023  -1.260  0.20790    
## `JobRoleLaboratory Technician`  -595.611    169.362  -3.517  0.00046 ***
## JobRoleManager                  3991.649    229.479  17.394  < 2e-16 ***
## `JobRoleManufacturing Director`  136.295    167.359   0.814  0.41565    
## `JobRoleResearch Director`      3973.272    216.235  18.375  < 2e-16 ***
## `JobRoleResearch Scientist`     -322.429    169.632  -1.901  0.05767 .  
## `JobRoleSales Executive`         -65.567    144.365  -0.454  0.64982    
## `JobRoleSales Representative`   -413.192    212.294  -1.946  0.05194 .  
## TotalWorkingYears                 51.888     10.345   5.016 6.42e-07 ***
## YearsAtCompany                    -6.050      8.052  -0.751  0.45267    
## Age                               -1.231      5.496  -0.224  0.82282    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 1064 on 857 degrees of freedom
## Multiple R-squared:  0.9472,	Adjusted R-squared:  0.9465 
## F-statistic:  1282 on 12 and 857 DF,  p-value: < 2.2e-16
```

```r
# Results including RMSE of final model
emp_salary_rf.step$results
```

```
##   intercept    RMSE Rsquared      MAE   RMSESD RsquaredSD    MAESD
## 1      TRUE 1075.59 0.945446 822.5236 51.53372 0.00643905 23.17155
```


### MonthlyIncome Predictions


*Training Data*

We can see prediction error for Random Forest, 0.1652, vs the Linear Regression, 0.2112. Thus we will use the RF model on our final data


```r
# generating predictions on test data
employee.df$MonthlyIncome_LM <- predict(emp_salary.step, newdata = employee.df)
employee.df$MonthlyIncome_RF <- predict(emp_salary_rf.step, newdata = employee.df)

# Prediction Error for Linear Regression
RMSE(employee.df$MonthlyIncome_LM, employee.df$MonthlyIncome) / mean(employee.df$MonthlyIncome)
```

```
## [1] 0.2112216
```

```r
# Prediction Error for Linear Regression
RMSE(employee.df$MonthlyIncome_RF, employee.df$MonthlyIncome) / mean(employee.df$MonthlyIncome)
```

```
## [1] 0.1651993
```

*Test Data*

We run the model on the test data sets that do not have the MonthlyIncome parameter

NOTE: Was not sure if I need to include BOTH the Regression and Random Forest predictions since homework doc said "You MUST use linear regression but MAY include additional models." So just to be safe I'm including both:

* MonthlyIncome_LM = Predictions for Regression
* MonthlyIncome_RF = Predictions for Random Forest



```r
# loading prediction data
salary_pred.df <- read.csv(
  "https://raw.githubusercontent.com/jotsap/CaseStudy2DDS/master/data/CaseStudy2CompSet_NoSalary.csv", 
  sep = ",", 
  header = TRUE)
str(salary_pred.df)
```

```
## 'data.frame':	300 obs. of  35 variables:
##  $ ID                      : int  871 872 873 874 875 876 877 878 879 880 ...
##  $ Age                     : int  43 33 55 36 27 39 33 21 30 51 ...
##  $ Attrition               : Factor w/ 2 levels "No","Yes": 1 1 2 1 1 2 1 2 1 1 ...
##  $ BusinessTravel          : Factor w/ 3 levels "Non-Travel","Travel_Frequently",..: 2 3 3 1 3 3 1 2 2 3 ...
##  $ DailyRate               : int  1422 461 267 1351 1302 895 750 251 1312 1405 ...
##  $ Department              : Factor w/ 3 levels "Human Resources",..: 3 2 3 2 2 3 3 2 2 2 ...
##  $ DistanceFromHome        : int  2 13 13 9 19 5 22 10 23 11 ...
##  $ Education               : int  4 1 4 4 3 3 2 2 3 2 ...
##  $ EducationField          : Factor w/ 6 levels "Human Resources",..: 2 2 3 2 5 6 3 2 2 6 ...
##  $ EmployeeCount           : int  1 1 1 1 1 1 1 1 1 1 ...
##  $ EmployeeNumber          : int  1849 995 1372 1949 1619 42 160 1279 159 1367 ...
##  $ EnvironmentSatisfaction : int  1 2 1 1 4 4 3 1 1 4 ...
##  $ Gender                  : Factor w/ 2 levels "Female","Male": 2 1 2 2 2 2 2 1 2 1 ...
##  $ HourlyRate              : int  92 53 85 66 67 56 95 45 96 82 ...
##  $ JobInvolvement          : int  3 3 4 4 2 3 3 2 1 2 ...
##  $ JobLevel                : int  2 1 4 1 1 2 2 1 1 4 ...
##  $ JobRole                 : Factor w/ 9 levels "Healthcare Representative",..: 8 7 8 3 3 9 8 3 7 5 ...
##  $ JobSatisfaction         : int  4 4 3 2 1 4 2 3 3 2 ...
##  $ MaritalStatus           : Factor w/ 3 levels "Divorced","Married",..: 2 3 3 2 1 2 2 3 1 3 ...
##  $ MonthlyRate             : int  19246 17241 9277 9238 16290 3335 15480 25308 22310 24439 ...
##  $ NumCompaniesWorked      : int  1 3 6 1 1 3 0 1 1 3 ...
##  $ Over18                  : Factor w/ 1 level "Y": 1 1 1 1 1 1 1 1 1 1 ...
##  $ OverTime                : Factor w/ 2 levels "No","Yes": 1 1 2 1 1 1 1 1 1 1 ...
##  $ PercentSalaryHike       : int  20 18 17 22 11 14 13 20 25 16 ...
##  $ PerformanceRating       : int  4 3 3 4 3 3 3 4 4 3 ...
##  $ RelationshipSatisfaction: int  3 1 3 2 1 3 1 3 3 2 ...
##  $ StandardHours           : int  80 80 80 80 80 80 80 80 80 80 ...
##  $ StockOptionLevel        : int  1 0 0 0 2 1 1 0 3 0 ...
##  $ TotalWorkingYears       : int  7 5 24 5 7 19 8 2 10 29 ...
##  $ TrainingTimesLastYear   : int  5 4 2 3 3 6 2 2 2 1 ...
##  $ WorkLifeBalance         : int  3 3 2 3 3 4 4 1 2 2 ...
##  $ YearsAtCompany          : int  7 3 19 5 7 1 7 2 10 5 ...
##  $ YearsInCurrentRole      : int  7 2 7 4 7 0 7 2 7 2 ...
##  $ YearsSinceLastPromotion : int  7 0 3 0 0 0 0 2 0 0 ...
##  $ YearsWithCurrManager    : int  7 2 8 2 7 0 7 2 9 3 ...
```

```r
head(salary_pred.df) 
```

```
##    ID Age Attrition    BusinessTravel DailyRate             Department
## 1 871  43        No Travel_Frequently      1422                  Sales
## 2 872  33        No     Travel_Rarely       461 Research & Development
## 3 873  55       Yes     Travel_Rarely       267                  Sales
## 4 874  36        No        Non-Travel      1351 Research & Development
## 5 875  27        No     Travel_Rarely      1302 Research & Development
## 6 876  39       Yes     Travel_Rarely       895                  Sales
##   DistanceFromHome Education   EducationField EmployeeCount EmployeeNumber
## 1                2         4    Life Sciences             1           1849
## 2               13         1    Life Sciences             1            995
## 3               13         4        Marketing             1           1372
## 4                9         4    Life Sciences             1           1949
## 5               19         3            Other             1           1619
## 6                5         3 Technical Degree             1             42
##   EnvironmentSatisfaction Gender HourlyRate JobInvolvement JobLevel
## 1                       1   Male         92              3        2
## 2                       2 Female         53              3        1
## 3                       1   Male         85              4        4
## 4                       1   Male         66              4        1
## 5                       4   Male         67              2        1
## 6                       4   Male         56              3        2
##                 JobRole JobSatisfaction MaritalStatus MonthlyRate
## 1       Sales Executive               4       Married       19246
## 2    Research Scientist               4        Single       17241
## 3       Sales Executive               3        Single        9277
## 4 Laboratory Technician               2       Married        9238
## 5 Laboratory Technician               1      Divorced       16290
## 6  Sales Representative               4       Married        3335
##   NumCompaniesWorked Over18 OverTime PercentSalaryHike PerformanceRating
## 1                  1      Y       No                20                 4
## 2                  3      Y       No                18                 3
## 3                  6      Y      Yes                17                 3
## 4                  1      Y       No                22                 4
## 5                  1      Y       No                11                 3
## 6                  3      Y       No                14                 3
##   RelationshipSatisfaction StandardHours StockOptionLevel
## 1                        3            80                1
## 2                        1            80                0
## 3                        3            80                0
## 4                        2            80                0
## 5                        1            80                2
## 6                        3            80                1
##   TotalWorkingYears TrainingTimesLastYear WorkLifeBalance YearsAtCompany
## 1                 7                     5               3              7
## 2                 5                     4               3              3
## 3                24                     2               2             19
## 4                 5                     3               3              5
## 5                 7                     3               3              7
## 6                19                     6               4              1
##   YearsInCurrentRole YearsSinceLastPromotion YearsWithCurrManager
## 1                  7                       7                    7
## 2                  2                       0                    2
## 3                  7                       3                    8
## 4                  4                       0                    2
## 5                  7                       0                    7
## 6                  0                       0                    0
```

```r
# generating predictions on test data
salary_pred.df$MonthlyIncome_LM <- predict(emp_salary.step, newdata = salary_pred.df)
salary_pred.df$MonthlyIncome_RF <- predict(emp_salary_rf.step, newdata = salary_pred.df)
```


## Attrition

Now we will focus on Attrition, first using K-Nearest Neighbor [KNN], and then comparing to a Random Forest model

First we need to split the data into a training & testing set. 80% of our data will be used for the training set and will be used to create the model. The remaining 20% is for the test set, which will be used to validate actual values vs predicted values using our model. 

We need to measure the models: 

* Accuracy: correct results / all results
* Sensitivity: correctly predicted Attrition:No / All actual "No" 
* Specificity: correctly predicted Attrition:Yes / All actual "Yes" 


```r
### Creating 80/20 Training / Test Data Split 

attrition.vector <- createDataPartition(emp_attrition_forest.df$Attrition, p = 0.8, list = F)
attrition.train <- emp_attrition_forest.df[attrition.vector,] 
attrition.test <- emp_attrition_forest.df[-attrition.vector,]

# validate train and test sets
head(attrition.train)
```

```
##   MonthlyIncome Attrition Age    BusinessTravel             Department
## 1          4403        No  32     Travel_Rarely                  Sales
## 2         19626        No  40     Travel_Rarely Research & Development
## 3          9362        No  35 Travel_Frequently Research & Development
## 4         10422        No  32     Travel_Rarely                  Sales
## 5          3760        No  24 Travel_Frequently Research & Development
## 7          2127        No  41     Travel_Rarely Research & Development
##   DistanceFromHome Education   EducationField EnvironmentSatisfaction
## 1               13         4    Life Sciences                       2
## 2               14         3          Medical                       3
## 3               18         2    Life Sciences                       3
## 4                1         4        Marketing                       3
## 5                2         1 Technical Degree                       1
## 7                5         5          Medical                       2
##   Gender JobInvolvement JobLevel                JobRole JobSatisfaction
## 1   Male              3        2        Sales Executive               4
## 2   Male              2        5      Research Director               3
## 3   Male              3        3 Manufacturing Director               4
## 4 Female              3        3        Sales Executive               4
## 5 Female              3        1     Research Scientist               4
## 7   Male              4        1     Research Scientist               3
##   MaritalStatus NumCompaniesWorked OverTime PercentSalaryHike
## 1      Divorced                  2       No                11
## 2        Single                  1       No                14
## 3        Single                  2       No                11
## 4       Married                  1       No                19
## 5        Single                  1      Yes                13
## 7       Married                  2      Yes                12
##   RelationshipSatisfaction StockOptionLevel TotalWorkingYears
## 1                        3                1                 8
## 2                        1                0                21
## 3                        3                0                10
## 4                        3                2                14
## 5                        3                0                 6
## 7                        1                0                 7
##   TrainingTimesLastYear WorkLifeBalance YearsAtCompany YearsInCurrentRole
## 1                     3               2              5                  2
## 2                     2               4             20                  7
## 3                     2               3              2                  2
## 4                     3               3             14                 10
## 5                     2               3              6                  3
## 7                     5               2              4                  2
##   YearsSinceLastPromotion YearsWithCurrManager
## 1                       0                    3
## 2                       4                    9
## 3                       2                    2
## 4                       5                    7
## 5                       1                    3
## 7                       0                    3
```

```r
head(attrition.test)
```

```
##    MonthlyIncome Attrition Age    BusinessTravel             Department
## 6           8793        No  27 Travel_Frequently Research & Development
## 12          2706        No  28        Non-Travel        Human Resources
## 14          2476        No  30     Travel_Rarely                  Sales
## 16          6932        No  31        Non-Travel                  Sales
## 19          2932        No  34     Travel_Rarely Research & Development
## 22          8120        No  48     Travel_Rarely                  Sales
##    DistanceFromHome Education   EducationField EnvironmentSatisfaction
## 6                10         2    Life Sciences                       4
## 12                1         2    Life Sciences                       3
## 14                2         1 Technical Degree                       3
## 16               20         2        Marketing                       4
## 19                2         4          Medical                       4
## 22                2         1        Marketing                       2
##    Gender JobInvolvement JobLevel                JobRole JobSatisfaction
## 6    Male              3        3 Manufacturing Director               1
## 12   Male              3        1        Human Resources               4
## 14   Male              3        1   Sales Representative               2
## 16 Female              3        2        Sales Executive               3
## 19   Male              4        1     Research Scientist               4
## 22   Male              4        2        Sales Executive               2
##    MaritalStatus NumCompaniesWorked OverTime PercentSalaryHike
## 6       Divorced                  1       No                21
## 12      Divorced                  1       No                15
## 14       Married                  1       No                18
## 16       Married                  1       No                13
## 19       Married                  0      Yes                14
## 22       Married                  3       No                12
##    RelationshipSatisfaction StockOptionLevel TotalWorkingYears
## 6                         3                2                 9
## 12                        2                1                 3
## 14                        1                1                 1
## 16                        4                1                 9
## 19                        1                3                 6
## 22                        4                0                12
##    TrainingTimesLastYear WorkLifeBalance YearsAtCompany YearsInCurrentRole
## 6                      4               2              9                  7
## 12                     2               3              3                  2
## 14                     3               3              1                  0
## 16                     2               2              9                  8
## 19                     3               3              5                  0
## 22                     3               3              2                  2
##    YearsSinceLastPromotion YearsWithCurrManager
## 6                        1                    7
## 12                       2                    2
## 14                       0                    0
## 16                       0                    0
## 19                       1                    2
## 22                       2                    2
```

### K-Nearest Neighbor

Overall when we run the model on the test data set we acheive above 85% for all categories.

       No Yes
  No  145   1
  Yes  21   7

Accuracy : 0.8736
Sensitivity : 0.8735         
Specificity : 0.8750 


```r
# control settings for KNN
# NOTE: needed in order to focus on specificity

train.knn <- trainControl(
  method = "repeatedcv",
  number = 5,
  repeats = 25,
  summaryFunction = twoClassSummary,
  classProbs = TRUE
)

# KNN on Training Set

attrition.knn <- train(
  Attrition ~ .,
  data = attrition.train,
  method = "knn",
  metric = "Spec",
  trControl = train.knn,
  preProcess = c("center","scale"),
  tuneLength = 6
)

# Adding predictions to Test Data
predict(attrition.knn, newdata = attrition.test ) -> attrition.test$Attrition_KNN
# creating confusion matrix
confusionMatrix(
  table(attrition.test$Attrition, attrition.test$Attrition_KNN )
)
```

```
## Confusion Matrix and Statistics
## 
##      
##        No Yes
##   No  142   4
##   Yes  23   5
##                                           
##                Accuracy : 0.8448          
##                  95% CI : (0.7823, 0.8952)
##     No Information Rate : 0.9483          
##     P-Value [Acc > NIR] : 1.000000        
##                                           
##                   Kappa : 0.2083          
##  Mcnemar's Test P-Value : 0.000532        
##                                           
##             Sensitivity : 0.8606          
##             Specificity : 0.5556          
##          Pos Pred Value : 0.9726          
##          Neg Pred Value : 0.1786          
##              Prevalence : 0.9483          
##          Detection Rate : 0.8161          
##    Detection Prevalence : 0.8391          
##       Balanced Accuracy : 0.7081          
##                                           
##        'Positive' Class : No              
## 
```


### Random Forest

Was very close for Accuracy and Specificity but performed worse in the Sensitivity category. This is due to the fact that the model misclassified one more employee as Attrition: No when they were actually Attrition: Yes

      No Yes
  No  144   2
  Yes  21   7

Accuracy : 0.8678 
Sensitivity : 0.8727          
Specificity : 0.7778 


```r
# Random Forest
train(Attrition ~ .,
  data = attrition.train,
  method = "rf",
  trControl = train.cv,
  importance = TRUE
) -> attrition.rf

# Adding predictions to Test Data
predict(attrition.rf, newdata = attrition.test ) -> attrition.test$Attrition_RF
# creating confusion matrix
confusionMatrix(
  table(attrition.test$Attrition, attrition.test$Attrition_RF )
)
```

```
## Confusion Matrix and Statistics
## 
##      
##        No Yes
##   No  143   3
##   Yes  19   9
##                                          
##                Accuracy : 0.8736         
##                  95% CI : (0.8149, 0.919)
##     No Information Rate : 0.931          
##     P-Value [Acc > NIR] : 0.997903       
##                                          
##                   Kappa : 0.3912         
##  Mcnemar's Test P-Value : 0.001384       
##                                          
##             Sensitivity : 0.8827         
##             Specificity : 0.7500         
##          Pos Pred Value : 0.9795         
##          Neg Pred Value : 0.3214         
##              Prevalence : 0.9310         
##          Detection Rate : 0.8218         
##    Detection Prevalence : 0.8391         
##       Balanced Accuracy : 0.8164         
##                                          
##        'Positive' Class : No             
## 
```


### Predictions for Attrition

As an additional validation step we then can run the two models across the entire data set and compare against the actual Attrition values

*Validating KNN on Entire Training Data Set*

Again the model performs will acheiving over 85% in all 3 categories


       No Yes
  No  724   6
  Yes 107  33
  
Accuracy : 0.8701
Sensitivity : 0.8712          
Specificity : 0.8462



```r
# generating predictions on test data
employee.df$Attrition_KNN <- predict(attrition.knn, newdata = employee.df)

# creating confusion matrix for KNN
confusionMatrix(
  table(employee.df$Attrition, employee.df$Attrition_KNN )
)
```

```
## Confusion Matrix and Statistics
## 
##      
##        No Yes
##   No  724   6
##   Yes 102  38
##                                          
##                Accuracy : 0.8759         
##                  95% CI : (0.8521, 0.897)
##     No Information Rate : 0.9494         
##     P-Value [Acc > NIR] : 1              
##                                          
##                   Kappa : 0.3641         
##  Mcnemar's Test P-Value : <2e-16         
##                                          
##             Sensitivity : 0.8765         
##             Specificity : 0.8636         
##          Pos Pred Value : 0.9918         
##          Neg Pred Value : 0.2714         
##              Prevalence : 0.9494         
##          Detection Rate : 0.8322         
##    Detection Prevalence : 0.8391         
##       Balanced Accuracy : 0.8701         
##                                          
##        'Positive' Class : No             
## 
```


*Validating Random Forest on Entire Training Data Set*

Here the Random Forest performs much higher, exceeding 95%. Because these values are so high, there may be a concern for overfitting. However we did utilize 5-fold Cross Validation in training the model to try prevent this issue.

Furthermore when we look at the ratio of "Yes" to "No" the KNN model strongly deviates from what we've seen up to this point



       No Yes
  No  728   2
  Yes  21 119

Accuracy : 0.9736
Sensitivity : 0.9720          
Specificity : 0.9835 



```r
# generating predictions on test data
employee.df$Attrition_RF <- predict(attrition.rf, newdata = employee.df)

# creating confusion matrix for RF
confusionMatrix(
  table(employee.df$Attrition, employee.df$Attrition_RF )
)
```

```
## Confusion Matrix and Statistics
## 
##      
##        No Yes
##   No  727   3
##   Yes  19 121
##                                          
##                Accuracy : 0.9747         
##                  95% CI : (0.962, 0.9841)
##     No Information Rate : 0.8575         
##     P-Value [Acc > NIR] : < 2.2e-16      
##                                          
##                   Kappa : 0.9018         
##  Mcnemar's Test P-Value : 0.001384       
##                                          
##             Sensitivity : 0.9745         
##             Specificity : 0.9758         
##          Pos Pred Value : 0.9959         
##          Neg Pred Value : 0.8643         
##              Prevalence : 0.8575         
##          Detection Rate : 0.8356         
##    Detection Prevalence : 0.8391         
##       Balanced Accuracy : 0.9752         
##                                          
##        'Positive' Class : No             
## 
```


*Test Data*

We run the model on the test data sets that do not have the Attrition parameter

NOTE: Was not sure if I need to include BOTH the KNN and Random Forest predictions since homework doc said "You MUST use linear regression but MAY include additional models." So just to be safe I'm including both:

* Attrition_KNN = Predictions for KNN
* Attrition_RF = Predictions for Random Forest 


```r
# loading prediction data
attrition_pred.df <- read.csv(
  "https://raw.githubusercontent.com/jotsap/CaseStudy2DDS/master/data/CaseStudy2CompSet_NoAttrition.csv", 
  sep = ",", 
  header = TRUE)
str(attrition_pred.df)
```

```
## 'data.frame':	300 obs. of  35 variables:
##  $ ID                      : int  1171 1172 1173 1174 1175 1176 1177 1178 1179 1180 ...
##  $ Age                     : int  35 33 26 55 29 51 52 39 31 31 ...
##  $ BusinessTravel          : Factor w/ 3 levels "Non-Travel","Travel_Frequently",..: 3 3 3 3 3 2 1 3 3 2 ...
##  $ DailyRate               : int  750 147 1330 1311 1246 1456 585 1387 1062 534 ...
##  $ Department              : Factor w/ 3 levels "Human Resources",..: 2 1 2 2 3 2 3 2 2 2 ...
##  $ DistanceFromHome        : int  28 2 21 2 19 1 29 10 24 20 ...
##  $ Education               : int  3 3 3 3 3 4 4 5 3 3 ...
##  $ EducationField          : Factor w/ 6 levels "Human Resources",..: 2 1 4 2 2 4 2 4 4 2 ...
##  $ EmployeeCount           : int  1 1 1 1 1 1 1 1 1 1 ...
##  $ EmployeeNumber          : int  1596 1207 1107 505 1497 145 2019 1618 1252 587 ...
##  $ EnvironmentSatisfaction : int  2 2 1 3 3 1 1 2 3 1 ...
##  $ Gender                  : Factor w/ 2 levels "Female","Male": 2 2 2 1 2 1 2 2 1 2 ...
##  $ HourlyRate              : int  46 99 37 97 77 30 40 76 96 66 ...
##  $ JobInvolvement          : int  4 3 3 3 2 2 3 3 2 3 ...
##  $ JobLevel                : int  2 1 1 4 2 3 1 2 2 3 ...
##  $ JobRole                 : Factor w/ 9 levels "Healthcare Representative",..: 3 2 3 4 8 1 9 5 1 1 ...
##  $ JobSatisfaction         : int  3 3 3 4 3 1 4 1 1 3 ...
##  $ MaritalStatus           : Factor w/ 3 levels "Divorced","Married",..: 2 2 1 3 1 3 1 2 3 2 ...
##  $ MonthlyIncome           : int  3407 3600 2377 16659 8620 7484 3482 5377 6812 9824 ...
##  $ MonthlyRate             : int  25348 8429 19373 23258 23757 25796 19788 3835 17198 22908 ...
##  $ NumCompaniesWorked      : int  1 1 1 2 1 3 2 2 1 3 ...
##  $ Over18                  : Factor w/ 1 level "Y": 1 1 1 1 1 1 1 1 1 1 ...
##  $ OverTime                : Factor w/ 2 levels "No","Yes": 1 1 1 2 1 1 1 1 1 1 ...
##  $ PercentSalaryHike       : int  17 13 20 13 14 20 15 13 19 12 ...
##  $ PerformanceRating       : int  3 3 4 3 3 4 3 3 3 3 ...
##  $ RelationshipSatisfaction: int  4 4 3 3 3 3 2 4 2 1 ...
##  $ StandardHours           : int  80 80 80 80 80 80 80 80 80 80 ...
##  $ StockOptionLevel        : int  2 1 1 0 2 0 2 3 0 0 ...
##  $ TotalWorkingYears       : int  10 5 1 30 10 23 16 10 10 12 ...
##  $ TrainingTimesLastYear   : int  3 2 0 2 3 1 3 3 2 2 ...
##  $ WorkLifeBalance         : int  2 3 2 3 3 2 2 3 3 3 ...
##  $ YearsAtCompany          : int  10 5 1 5 10 13 9 7 10 1 ...
##  $ YearsInCurrentRole      : int  9 4 1 4 7 12 8 7 9 0 ...
##  $ YearsSinceLastPromotion : int  6 1 0 1 0 12 0 7 1 0 ...
##  $ YearsWithCurrManager    : int  8 4 0 2 4 8 0 7 8 0 ...
```

```r
head(attrition_pred.df) 
```

```
##     ID Age    BusinessTravel DailyRate             Department
## 1 1171  35     Travel_Rarely       750 Research & Development
## 2 1172  33     Travel_Rarely       147        Human Resources
## 3 1173  26     Travel_Rarely      1330 Research & Development
## 4 1174  55     Travel_Rarely      1311 Research & Development
## 5 1175  29     Travel_Rarely      1246                  Sales
## 6 1176  51 Travel_Frequently      1456 Research & Development
##   DistanceFromHome Education  EducationField EmployeeCount EmployeeNumber
## 1               28         3   Life Sciences             1           1596
## 2                2         3 Human Resources             1           1207
## 3               21         3         Medical             1           1107
## 4                2         3   Life Sciences             1            505
## 5               19         3   Life Sciences             1           1497
## 6                1         4         Medical             1            145
##   EnvironmentSatisfaction Gender HourlyRate JobInvolvement JobLevel
## 1                       2   Male         46              4        2
## 2                       2   Male         99              3        1
## 3                       1   Male         37              3        1
## 4                       3 Female         97              3        4
## 5                       3   Male         77              2        2
## 6                       1 Female         30              2        3
##                     JobRole JobSatisfaction MaritalStatus MonthlyIncome
## 1     Laboratory Technician               3       Married          3407
## 2           Human Resources               3       Married          3600
## 3     Laboratory Technician               3      Divorced          2377
## 4                   Manager               4        Single         16659
## 5           Sales Executive               3      Divorced          8620
## 6 Healthcare Representative               1        Single          7484
##   MonthlyRate NumCompaniesWorked Over18 OverTime PercentSalaryHike
## 1       25348                  1      Y       No                17
## 2        8429                  1      Y       No                13
## 3       19373                  1      Y       No                20
## 4       23258                  2      Y      Yes                13
## 5       23757                  1      Y       No                14
## 6       25796                  3      Y       No                20
##   PerformanceRating RelationshipSatisfaction StandardHours
## 1                 3                        4            80
## 2                 3                        4            80
## 3                 4                        3            80
## 4                 3                        3            80
## 5                 3                        3            80
## 6                 4                        3            80
##   StockOptionLevel TotalWorkingYears TrainingTimesLastYear WorkLifeBalance
## 1                2                10                     3               2
## 2                1                 5                     2               3
## 3                1                 1                     0               2
## 4                0                30                     2               3
## 5                2                10                     3               3
## 6                0                23                     1               2
##   YearsAtCompany YearsInCurrentRole YearsSinceLastPromotion
## 1             10                  9                       6
## 2              5                  4                       1
## 3              1                  1                       0
## 4              5                  4                       1
## 5             10                  7                       0
## 6             13                 12                      12
##   YearsWithCurrManager
## 1                    8
## 2                    4
## 3                    0
## 4                    2
## 5                    4
## 6                    8
```

```r
# generating predictions on test data
attrition_pred.df$Attrition_KNN <- predict(attrition.knn, attrition_pred.df)
attrition_pred.df$Attrition_RF <- predict(attrition.rf, attrition_pred.df)
```



## Conclusion

For determining Attrition, the most likely profile based on our analysis is the following.

Candidates with "Yes" in the OverTime field are more likely to leave. We are assuming these are non-exempt employees, however this is not explicitly stated for the data set

Candidates that make less than $30,000 per year are more likely to leave. There is evidence of a correlation between MonthlyIncome and Attrition

JobRoles can effect in both ways. Sales Reps are more likely to leave, while Research and Marketing Directors are more likely to stay

MaritalStatus can also effect in both ways. Single employees are more likely to leave, while Divorced employees are more likely to stay





