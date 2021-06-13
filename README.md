# Pilot Study on Partitioning
Repository for storing materials and data from the pilot study on partitioning to accompany ["Sources of Philosophical Intuitions: Towards a Model of Intuition Generation"](https://github.com/DominikDziedzic/IntuitionGenerationProject) project.

This study examines the interplay between pre-experimentally and experimentally acquired knowledge after limited training. David Kaplan’s ‘Carnap vs. Agnew’ case (1978, p. 239) concerning the reference of demonstratives has been converted to experimental materials and presented to participants in one of three conditions.

**The online survey tool used in this study can be accessed here: [On the Use of English Demonstrative "That" (Partitioning)](http://kognilab.pl/ls3/index.php/776326?newtest=Y&lang=en).**

Content of the repository (after opening each file, right-click and select Save as):
- **Raw data** 
  - [in .txt](https://raw.githubusercontent.com/DominikDziedzic/PilotStudyPartitioning/main/data.txt) 
  - [in .csv](https://raw.githubusercontent.com/DominikDziedzic/PilotStudyPartitioning/main/data.csv)
- **Experimental materials**
  -  [in .docx]()
  -  [in .pdf]() 
- [**Source code in .R**]()

# Analysis

The results of statistical analyses are presented below: 
- [**Analysis of Variance**](https://github.com/DominikDziedzic/PilotStudyPartitioning#analysis-of-variance)
- [**Frequentist Linear Regression**](https://github.com/DominikDziedzic/PilotStudyPartitioning#frequentist-linear-regression)
- [**Bayesian Linear Regression**](https://github.com/DominikDziedzic/PilotStudyPartitioning#bayesian-linear-regression)
- [References](https://github.com/DominikDziedzic/PilotStudyPartitioning#references)

## Analysis of Variance

### Required packages
Run the following code in R to install the required packages:

``` r
install.packages("") TODO
```
### Import data
Download raw data files in [.txt](https://raw.githubusercontent.com/DominikDziedzic/PilotStudyPartitioning/main/data.txt) or [.csv format](https://raw.githubusercontent.com/DominikDziedzic/PilotStudyPartitioning/main/data.csv) and run the following in R to import data:

``` r
# a) if in .txt:
data <- read.delim(file.choose())
# b) if in .csv:
data <- read.csv(file.choose(), sep = ";")

attach(data) # attach your data, so that objects in the database can be accessed by giving their names
data$condition <- as.factor(data$condition) #format the dataset
data$training <- as.factor(data$training)
```
Let's review the dataset:

``` r
str(data)
# 'data.frame':	45 obs. of  3 variables:
# $ condition: Factor w/ 3 levels "c","p","u": 2 2 2 2 2 2 2 2 2 2 ...
# $ training : Factor w/ 2 levels "0","1": 2 2 1 1 1 2 1 1 1 2 ...
# $ final    : int  0 0 0 0 0 1 0 0 0 0 ...
```
The dataset consists of two IVs (i.e., "condition" and "training") and the DV ("final" answers). The length of the dataset is 45. Assignment to conditions was random, with 15 participants in the control condition, 13 in the unprimed condition, and 17 in the primed. Definitions of the conditions are as follow:
- In **the primed condition**, the participant reads a scenario, and then answers a question: _When David uses the expression “That”, is he talking about: (A) The picture of Rudolf Carnap? (B) The picture of Elvis Presley?_ Next, the training begins. The training is primed in this condition, i.e., the questionnaire tracks the initial answers and displays the reasons _for_ the initial answers. During training, the participant categorizes the provided reasons as the reasons for an answer and receives feedback. The training phase is followed by the transfer phase, in which a random counter-reason is displayed to encourage the reorganization of acquired knowledge. This condition concludes with the final answers to the scenario, again: _When David uses the expression “That”, is he talking about: (A) The picture of Rudolf Carnap? (B) The picture of Elvis Presley?_
- In **the unprimed condition**, the questionnaire first displays a scenario, but instead of displaying a question, the training phase follows immediately after. Participants in this condition are randomly assigned to one of the possible answers to the scenario and are trained on the subsequent reasons _for_ that answer. The training phase is followed by the transfer phase (defined as above). The unprimed condition concludes with the final answer to the scenario.
- In **the control condition**, the questionnaire first displays a scenario, and then the training phase begins. There is no transfer phase in this condition: The training phase is followed by the final answer to the scenario.

Coding:
- **$ condition:**
  - "p" = primed
  - "u" = unprimed
  - "c" = control
- **$ training/final:**
  - "0" = The picture of Rudolf Carnap
  - "1" = The picture of Elvis Presley

### Calculate the means of the responses

``` r
library("dplyr")
group_by(data, condition, training) %>%
  summarise(
    count = n(),
    mean = mean(final, na.rm = TRUE),
    sd = sd(final, na.rm = TRUE)
  )
#   condition training count   mean    sd
#   <fct>     <fct>    <int>  <dbl> <dbl>
# 1 c         0           11 0.0909 0.302
# 2 c         1            4 1      0    
# 3 p         0           10 0.2    0.422
# 4 p         1            7 0.143  0.378
# 5 u         0            5 0.2    0.447
# 6 u         1            8 0.875  0.354
```
### Two-Way 3 (condition) × 2 (training stimulus) ANOVA

``` r
table(data$condition, data$training) # unbalanced design
#     0  1
#  c 11  4
#  p 10  7
#  u  5  8
```
Method 1: basic R function aov()
``` r
model0 <- aov(final ~ condition * training, data = data)
summary(model0)
#                    Df Sum Sq Mean Sq F value   Pr(>F)    
# condition           2  1.430  0.7151   5.532 0.007671 ** 
# training            1  2.007  2.0068  15.525 0.000327 ***
# condition:training  2  1.833  0.9164   7.089 0.002365 ** 
# Residuals          39  5.041  0.1293                     
# ---
# Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```
The main effects of condition, training stimulus, as well as the condition × stimulus type interaction, are significant.

``` r
effectsize::eta_squared(model0) # partial eta's squared from aov()
# Parameter          | Eta2 (partial) |       90% CI
# --------------------------------------------------
# condition          |           0.22 | [0.04, 0.39]
# training           |           0.28 | [0.10, 0.46]
# condition:training |           0.27 | [0.07, 0.43]
```
However, since the design is unbalanced let's adjust the calculations to produce Type-III sums of squares ("Type-III Sums of Squares", 2008):
``` r
options(contrasts = c("contr.sum", "contr.poly"))
model0 <- aov(final ~ condition * training, data = data)
drop1(model0,~.,test="F")
#                    Df Sum of Sq    RSS     AIC F value    Pr(>F)    
# <none>                          5.0412 -86.506                      
# condition           2    1.3381 6.3794 -79.912  5.1760  0.010146 *  
# training            1    2.5656 7.6069 -69.992 19.8483 6.866e-05 ***
# condition:training  2    1.8328 6.8740 -76.551  7.0895  0.002365 ** 
# ---
# Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```
The main effect of condition is still significant, though less so (p < 0.05). Let's compare these results with the results obtained by another method.

Method 2: function anova_test() in "rstatix" package
``` r
model1 <- anova_test(data, final ~ condition * training, type = 3, effect.size = "ges")
model1
#               Effect DFn DFd      F        p p<.05   ges
# 1          condition   2  39  5.176 1.00e-02     * 0.210
# 2           training   1  39 19.848 6.87e-05     * 0.337
# 3 condition:training   2  39  7.089 2.00e-03     * 0.267
```
The main effect of condition is significant according to the anova_test() method. Since all tests returned significant two-way interaction let's investigate the simple main effect of condition.
``` r
model <- lm(final ~ condition * training, data = data)
data %>%
  group_by(training) %>%
  anova_test(final ~ condition, error = model)
```
In







## Frequentist Linear Regression

## Bayesian Linear Regression

# References

- Kaplan, D. (1978). Dthat. In P. Cole (Ed.), _Syntax and Semantics: Pragmatics_ (pp. 221–243). New York: Academic Press.
- Type-III Sums of Squares. (2008). Retrieved from: http://myowelt.blogspot.com/2008/05/obtaining-same-anova-results-in-r-as-in.html
