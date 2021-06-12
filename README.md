# Pilot Study on Partitioning
Repository for storing materials and data from the pilot study on partitioning to accompany ["Sources of Philosophical Intuitions: Towards a Model of Intuition Generation"](https://github.com/DominikDziedzic/IntuitionGenerationProject) project.

## Required packages
Run the following code in R to install the required packages:

``` r
install.packages("") TODO
```
## Import data
Download raw data files in [.txt](https://raw.githubusercontent.com/DominikDziedzic/PilotStudyPartitioning/main/data.txt) or [.csv format](https://raw.githubusercontent.com/DominikDziedzic/PilotStudyPartitioning/main/data.csv) and run the following in R to import data:

``` r
# a) if in .txt:
data <- read.delim(file.choose())
# b) if in .csv:
data <- read.csv(file.choose(), sep = ";")

attach(data) # attach your data, so that objects in the database can be accessed by simply giving their names
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
The dataset consists of two IVs (i.e., "condition" and "training") and a DV ("final"). The final sample is 45.  
