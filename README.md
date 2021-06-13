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

## Frequentist Linear Regression

## Bayesian Linear Regression

# References

- Kaplan, D. (1978). Dthat. In P. Cole (Ed.), _Syntax and Semantics: Pragmatics_ (pp. 221–243). New York: Academic Press.
