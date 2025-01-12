## Extrating Web Data for Trade Show Prospecting

**Project description:** This project is code which I wrote and used throughout my time as a business developer/account manager at Infopro Digital. Often times, B2B trade shows will offer a functionality allowing you to download exhibitor data in an excel speadsheet. This is however not always the case, and it was common for sales people to take down infomation manually. This script allowed myself and my team to quickly and easily get the information needed for proper preparation of our trade show prospection.
### 1. Loading necessary packages

Here we are loading in all the necessary packages for this projet.

```{r}
#install.packages(c("titanic","tidyverse","caret","mice","VIM","MASS","ranger","randomForest","RColorBrewer","rpart","rattle","e1071","esquisse"))
#install.packages(c("XML","rpart.plot"))
#install.packages("rattle", repos="https://rattle.togaware.com", type="source")
library(titanic)
library(tidyverse)
library(caret)
library(mice) 
library(VIM)
library(MASS)
library(ranger)
library(randomForest)
library(RColorBrewer)
library(rpart)
library(rattle)
library(e1071)
library(esquisse)
library(kernlab)
library(tidymodels)
library(ROCR)
```

### 2. Loading in our data set

```{r}
credit = read_csv("cs.csv")
```

### 3. Structure and summary of our data

```{R}
str(credit)
summary(credit)
```
