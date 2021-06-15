# Pilot Study on Partitioning
Repository for storing materials and data from the pilot study on partitioning to accompany ["Sources of Philosophical Intuitions: Towards a Model of Intuition Generation"](https://github.com/DominikDziedzic/IntuitionGenerationProject) project.

This study examines the interplay between pre-experimentally and experimentally acquired knowledge after limited training. David Kaplan’s ‘Carnap vs. Agnew’ case (1978, p. 239) concerning the reference of demonstratives has been converted to experimental materials and presented to participants in one of three conditions.

**The online survey tool used in this study can be accessed here: [On the Use of English Demonstrative "That" (Partitioning)](http://kognilab.pl/ls3/index.php/776326?newtest=Y&lang=en).**

Content of the repository (after opening each file, right-click and select Save as):
- **Raw data** 
  - [in .txt](https://raw.githubusercontent.com/DominikDziedzic/PilotStudyPartitioning/main/data.txt) 
  - [in .csv](https://raw.githubusercontent.com/DominikDziedzic/PilotStudyPartitioning/main/data.csv)
- **Experimental materials**
  -  [in .docx](https://github.com/DominikDziedzic/PilotStudyPartitioning/blob/main/Experimental%20Materials.docx?raw=true)
  -  [in .pdf](https://github.com/DominikDziedzic/PilotStudyPartitioning/raw/main/Experimental%20Materials.pdf) 
- **Source code in .R**
  - [Two-Way ANOVA](https://raw.githubusercontent.com/DominikDziedzic/PilotStudyPartitioning/main/Analysis%2C%20Two-Way%20ANOVA.R)
  - Frequentist and Bayesian Linear Regression()

# Analysis

The results of statistical analyses are presented below: 
- [**Analysis of Variance**](https://github.com/DominikDziedzic/PilotStudyPartitioning#analysis-of-variance)
- [**Frequentist Linear Regression**](https://github.com/DominikDziedzic/PilotStudyPartitioning#frequentist-linear-regression)
- [**Bayesian Linear Regression**](https://github.com/DominikDziedzic/PilotStudyPartitioning#bayesian-linear-regression)
- [**References**](https://github.com/DominikDziedzic/PilotStudyPartitioning#references)

## Analysis of Variance

### Required packages
Run the following code in R to install the required packages:
``` r
install.packages("dplyr")
install.packages("effectsize")
install.packages("rstatix")
```

Load the required packages:
``` r
library(dplyr)
library(effectsize)
library(rstatix)
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
The dataset consists of two IVs (i.e., `condition` and `training`) and the DV (`final` answers). The length of the dataset is 45. Assignment to conditions was random, with 15 participants in the control condition, 13 in the unprimed condition, and 17 in the primed. Definitions of the conditions are as follow:
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
### Two-Way 3 (`condition`) × 2 (`training stimulus`) ANOVA

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
The main effects of `condition`, `training stimulus`, as well as the `condition` × `stimulus` type interaction, are significant.

``` r
effectsize::eta_squared(model0) # partial eta squared values from aov()
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
The main effect of `condition` is still significant, though less so (p < 0.05). Let's compare these results with the results obtained by another method.

Method 2: function anova_test() in "rstatix" package
``` r
model1 <- anova_test(data, final ~ condition * training, type = 3, effect.size = "ges")
model1
#               Effect DFn DFd      F        p p<.05   ges
# 1          condition   2  39  5.176 1.00e-02     * 0.210
# 2           training   1  39 19.848 6.87e-05     * 0.337
# 3 condition:training   2  39  7.089 2.00e-03     * 0.267
```
The main effect of `condition` is significant according to the anova_test() method. Moreover, according to Cohen's standard thresholds (Cohen, 1988), the effect is medium for `condition` (generalized eta squared = 0.21) and large for `training` (generalized eta squared = 0.38). Since all tests returned significant two-way interaction let's investigate the simple main effect of `condition`.

Run one-way model of `condition` at each level of `training`:
``` r
model <- lm(final ~ condition * training, data = data)
data %>%
  group_by(training) %>%
  anova_test(final ~ condition, error = model)
#   training Effect      DFn   DFd      F       p `p<.05`   ges
# * <fct>    <chr>     <dbl> <dbl>  <dbl>   <dbl> <chr>   <dbl>
# 1 0        condition     2    39  0.292 0.748   ""      0.015
# 2 1        condition     2    39 10.4   0.00024 "*"     0.348
```
The simple main effect of `condition` is significant for `training` group 1 (p < 0.001). Let's now perform multiple pairwise comparisons between the `condition` groups by `training`.

Compare the score of the different `condition` levels by `training` levels:
``` r
pwc <- data %>% 
  group_by(training) %>%
  emmeans_test(final ~ condition, p.adjust.method = "bonferroni") 
pwc
#   training term      .y.   group1 group2    df statistic        p    p.adj p.adj.signif
# * <chr>    <chr>     <chr> <chr>  <chr>  <dbl>     <dbl>    <dbl>    <dbl> <chr>       
# 1 0        condition final c      p         39 -6.94e- 1 0.492    1        ns          
# 2 0        condition final c      u         39 -5.63e- 1 0.577    1        ns          
# 3 0        condition final p      u         39 -2.82e-15 1.00     1        ns          
# 4 1        condition final c      p         39  3.80e+ 0 0.000490 0.00147  **          
# 5 1        condition final c      u         39  5.68e- 1 0.573    1        ns          
# 6 1        condition final p      u         39 -3.93e+ 0 0.000333 0.000998 *** 
```
There're significant differences of response scores between the control and primed `conditions` grouped by `training` level 1 (adjusted p < 0.01), as well as between the primed and unprimed `conditions` grouped by `training` level 1 (adjusted p < 0.001).

For an even finer-grained comparison, run the following:
``` r
posthoc <- lsmeans(model0, # applies to model0
                   pairwise ~ condition * training, 
                   adjust="tukey")
posthoc
# $lsmeans
#  condition training lsmean    SE df lower.CL upper.CL
#  c         0        0.0909 0.108 39   -0.128    0.310
#  p         0        0.2000 0.114 39   -0.030    0.430
#  u         0        0.2000 0.161 39   -0.125    0.525
#  c         1        1.0000 0.180 39    0.636    1.364
#  p         1        0.1429 0.136 39   -0.132    0.418
#  u         1        0.8750 0.127 39    0.618    1.132

# Confidence level used: 0.95 

# $contrasts
#  contrast  estimate    SE df t.ratio p.value
#  c 0 - p 0  -0.1091 0.157 39 -0.694  0.9815 
#  c 0 - u 0  -0.1091 0.194 39 -0.563  0.9929 
#  c 0 - c 1  -0.9091 0.210 39 -4.331  0.0013 
#  c 0 - p 1  -0.0519 0.174 39 -0.299  0.9997 
#  c 0 - u 1  -0.7841 0.167 39 -4.693  0.0004 
#  p 0 - u 0   0.0000 0.197 39  0.000  1.0000 
#  p 0 - c 1  -0.8000 0.213 39 -3.761  0.0068 
#  p 0 - p 1   0.0571 0.177 39  0.323  0.9995 
#  p 0 - u 1  -0.6750 0.171 39 -3.958  0.0039 
#  u 0 - c 1  -0.8000 0.241 39 -3.317  0.0225 
#  u 0 - p 1   0.0571 0.211 39  0.271  0.9998 
#  u 0 - u 1  -0.6750 0.205 39 -3.293  0.0240 
#  c 1 - p 1   0.8571 0.225 39  3.804  0.0061 
#  c 1 - u 1   0.1250 0.220 39  0.568  0.9926 
#  p 1 - u 1  -0.7321 0.186 39 -3.935  0.0042 

# P value adjustment: tukey method for comparing a family of 6 estimates 
```

## Frequentist Linear Regression

``` r
data$condition <- as.numeric(as.factor(data$condition))
data$condition[data$condition == 2] = 0 # code: "primed" = 0, "unprimed" = 1, "control" = 2
data$condition[data$condition == 1] = 2
data$condition[data$condition == 3] = 1
```
Coding:
- "p" = primed = 0
- "u" = unprimed = 1 
- "c" = control = 2

Fit the model:
``` r
summary(lm(final ~ condition + training, data = data))
# Residuals:
#      Min       1Q   Median       3Q      Max 
# -0.65034 -0.26808 -0.03047  0.34966  0.96953 

# Coefficients:
#             Estimate Std. Error t value Pr(>|t|)    
# (Intercept)  0.03047    0.11289   0.270 0.788531    
# condition    0.11880    0.07461   1.592 0.118794    
# training     0.50106    0.12720   3.939 0.000303 ***
# ---
# Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

# Residual standard error: 0.4186 on 42 degrees of freedom
# Multiple R-squared:  0.2861,	Adjusted R-squared:  0.2521 
# F-statistic: 8.415 on 2 and 42 DF,  p-value: 0.0008444

model <- lm(final ~ condition + training, data = data)
```

Compute the eta squared values:
``` r
effectsize::eta_squared(model)
# Parameter | Eta2 (partial) |       90% CI
# -----------------------------------------
# condition |           0.03 | [0.00, 0.16]
# training  |           0.27 | [0.10, 0.44]
```

Compute beta coefficients:
``` r
dataStand <- lapply(data, scale) # standardizes all variables
modelStand <- lm(final ~ condition + training, data = dataStand)
beta_coef <- coef(modelStand)
beta_coef
#  (Intercept)    condition     training 
#  5.611263e-19 2.090019e-01 5.170052e-01
```
The linear relationship between `final` responses (DV) and `training` (IV) is positive and significant (insignificant relationship between `final` responses and `condition`). `Final` reference judgments were significantly correlated  with `training` (b = 0.501, t(42) = 3.939, p < 0.001, eta^2 = 0.27, beta = 5.170). `Condition`: b = 0.119, t(42) = 1.592, p > 0.1, eta^2 = 0.03, beta = 2.090. Together, the two variables explain R^2 = 0.29% of the variability in `final` reference judgments, which is a large effect (Cohen, 1988).

## Bayesian Linear Regression

Fit the model and print the summary of posterior distribution:
``` r
model <- stan_glm(final ~ condition + training, data = data)
posteriorsDs <- describe_posterior(model)
print_md(posteriorsDs, digits = 2)
# [1] "Table: Summary of Posterior Distribution"                                               
# [2] ""                                                                                       
# [3] "|Parameter   | Median|       95% CI |    pd |         ROPE | % in ROPE| Rhat |    ESS |"
# [4] "|:-----------|------:|:-------------|:------|:-------------|---------:|:-----|:-------|"
# [5] "|(Intercept) |   0.03|[-0.18, 0.27] |61.50% |[-0.05, 0.05] |    33.65%|1.001 |4634.00 |"
# [6] "|condition   |   0.12|[-0.04, 0.26] |94.30% |[-0.05, 0.05] |    16.63%|1.000 |4127.00 |"
# [7] "|training    |   0.50|[ 0.24, 0.74] |  100% |[-0.05, 0.05] |        0%|1.000 |4372.00 |"
```

Extracting the posteriors:
``` r
posteriors <- insight::get_parameters(model)
```

Point-estimate (i.e., median in this case; similar to b in frequentist regression):
``` r
median(posteriors$condition)
# [1] 0.1167944
median(posteriors$training)
# [1] 0.4969569
```
`condition`: at 0.117, there is 50% chance that the true effect is higher and 50% chance that the effect is lower.
`training`: at 0.497, there is 50% chance that the true effect is higher and 50% chance that the effect is lower.

Compute the credible intervals (similar to frequentist confidence intervals). Use 89% CIs instead of 95% CIs (as in frequentist framework), as 89% level gives more stable results:
``` r
hdi(posteriors$condition, ci = 0.89)
# 89% HDI: [ 0.00, 0.24]
hdi(posteriors$training, ci = 0.89)
89% HDI: [0.30, 0.71]
```
`condition`: the effect has 89% chance of falling within the [0.00, 0.24] range.
`training`: the effect has 89% chance of falling within the [0.30, 0.71] range.

Effect significance in terms of ROPE (Region of Practical Equivalence, see Makowski, Ben-Shachar, Lüdecke, 2019):
``` r
ropeVal <- 0.1 * sd(data$final)
ropeRange <- c(-ropeVal, ropeVal)
ropeRange
# [1] -0.04840903  0.04840903

rope(posteriors$condition, range = ropeRange, ci = 0.89) # inside ROPE: 13.65%!
# inside ROPE
# -----------
# 13.65 %  
rope(posteriors$training, range = ropeRange, ci = 0.89)
# inside ROPE
# -----------
# 0.00 % 
```
Probability of direction (pd):
``` r
positivecondition <- posteriors %>%
  filter(condition > 0) %>% # select only positive values
  nrow() # Get length
positivecondition / nrow(posteriors) * 100
# [1] 93.8

positivetraining <- posteriors %>%
  filter(training > 0) %>% # select only positive values
  nrow() # Get length
positivetraining / nrow(posteriors) * 100
# [1] 99.68
```
`condition`: the effect is positive with a probability of 93.8%, , i.e., absence of true effect (Makowski, Ben-Shachar, Chen, Lüdecke, 2019b).
`training`: the effect is positive with a probability of 99.68%.

- The effect of `condition` (Median = 0.12, 95% CI [-0.03, 0.27]) has a 93.8% probability of being positive (> 0), 89.98% of being significant (> 0.02), and 36.52% of being large (> 0.15). The estimation successfully converged (Rhat = 1.000) and the indices are reliable (ESS = 4094).
- The effect of `training` (Median = 0.50, 95% CI [0.24, 0.75]) has a 99.68% probability of being positive (> 0), 99.98% of being significant (> 0.02), and 99.50% of being large (> 0.15). The estimation successfully converged (Rhat = 1.001) and the indices are reliable (ESS = 3633).

Describe the logistic model for comparison:
``` r
model <- stan_glm(final ~ condition + training, data = data, family = "binomial", refresh = 0)
describe_posterior(model, test = c("pd", "ROPE", "BF"))
# Parameter   | Median |         95% CI |     pd |          ROPE | % in ROPE |  Rhat |     ESS |    BF
# ----------------------------------------------------------------------------------------------------
# (Intercept) |  -2.74 | [-4.81, -1.12] |   100% | [-0.18, 0.18] |        0% | 1.001 | 2075.00 | 81.01
# condition   |   0.82 | [-0.10,  1.90] | 96.00% | [-0.18, 0.18] |     6.63% | 1.001 | 2443.00 | 0.768
# training    |   2.72 | [ 1.16,  4.52] |   100% | [-0.18, 0.18] |        0% | 1.001 | 2295.00 | 52.03

model_performance(model)
# ELPD    | ELPD_SE |  LOOIC | LOOIC_SE |   WAIC |    R2 |  RMSE | Sigma | Log_loss | Score_log | Score_spherical
# ---------------------------------------------------------------------------------------------------------------
# -25.804 |   5.039 | 51.608 |   10.078 | 51.489 | 0.318 | 0.387 | 1.000 |    0.495 |    -7.910 |           0.084
```

# References
- Ben-Shachar, M., Lüdecke, D., Makowski, D. (2020). effectsize: Estimation of Effect Size Indices and Standardized Parameters. _Journal of Open Source Software_, _5_(56), 2815. doi:10.21105/joss.02815
- Bürkner, P.-Ch. (2017). brms: An R Package for Bayesian Multilevel Models Using Stan. _Journal of Statistical Software_, _80_(1), 1-28. doi:10.18637/jss.v080.i01
- Cohen, J. (1988). Statistical Power Analysis for the Behavioral Sciences (2nd Ed.). Hillsdale, NJ: Laurence Erlbaum Associates.
- Goodrich, B., Gabry, J., Ali, I., Brilleman, S. (2020). rstanarm: Bayesian applied regression modeling via Stan. R package version 2.21.1. Retrieved from: https://mc-stan.org/rstanarm
- Kaplan, D. (1978). Dthat. In P. Cole (Ed.), _Syntax and Semantics: Pragmatics_ (pp. 221–243). New York: Academic Press.
- Kassambara, A. (2021). rstatix: Pipe-Friendly Framework for Basic Statistical Tests. R package version 0.7.0. Retrieved from: https://CRAN.R-project.org/package=rstatix
- Lenth, R. V. (2021). emmeans: Estimated Marginal Means, aka Least-Squares Means. R package version 1.6.1. Retrieved from: https://CRAN.R-project.org/package=emmeans
- Lüdecke, D., Waggoner, P., Makowski, D. (2019). insight: A Unified Interface to Access Information from ModelObjects in R. _Journal of Open Source Software_, *4*(38), 1412. doi:10.21105/joss.01412
- Lüdecke, et al., (2021). performance: An R Package for Assessment, Comparison and Testing of Statistical Models. _Journal of Open Source Software_, _6_(60), 3139. doi:10.21105/joss.03139
- Makowski, D., Ben-Shachar, M. S., Lüdecke, D. (2019a). bayestestR: Describing Effects and Their Uncertainty, Existence and Significance Within the Bayesian Framework. _Journal of Open Source Software_, _4_(40), 1541. doi:10.21105/joss.01541
- Makowski, D., Ben-Shachar, M. S., Chen, S. H. A., Lüdecke, D. (2019b). Indices of Effect Existence and Significance in the Bayesian Framework. _Frontiers in Psychology_, 10(2767). doi:10.3389/fpsyg.2019.02767
- Makowski, D., Ben-Shachar, M. S., Lüdecke, D. (2020a). *Estimation of Model-Based Predictions, Contrasts and Means*. CRAN.
- Makowski, D., Ben-Shachar, M. S., Lüdecke, D. (2020b). *The {easystats} collection of R packages*. GitHub.
- Morey, R. D., Rouder, J. N. (2018). BayesFactor: Computation of Bayes Factors for Common Designs. R package version 0.9.12-4.2. Retrieved from: https://CRAN.R-project.org/package=BayesFactor
- R Core Team (2021). R: A language and environment for statistical computing. R Foundation for Statistical Computing, Vienna, Austria. Retrieved from: https://www.R-project.org/
- Type-III Sums of Squares. (2008). Retrieved from: http://myowelt.blogspot.com/2008/05/obtaining-same-anova-results-in-r-as-in.html
- Wickham, H., François, R., Henry, L., and Müller, K. (2021). dplyr: A Grammar of Data Manipulation. R package version 1.0.6. Retrieved from: https://CRAN.R-project.org/package=dplyr
