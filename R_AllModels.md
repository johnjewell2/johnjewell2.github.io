## Machine Learning with R

**Project description:** This project is a collection of three model types explored throughout my studies (logistic regression, classification trees, random forest) assembled all into one place. For each model, we we will working with data from the Credit Score data set.

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

### 4. Factor conversion

```{r}
credit = credit %>% mutate(SeriousDlqin2yrs = as_factor(SeriousDlqin2yrs)) %>% 
  mutate(SeriousDlqin2yrs = fct_recode(SeriousDlqin2yrs, "No" = "0", "Yes" = "1" )) 

str(credit)
```

### 5. Taking a closer look

Here I am taking another look at our data to check for any outliers or missing data in our data set. For larger data sets this can be done efficiently using the missingna package, but for this data set our summmary function works nicely.

```{r}
summary(credit)
```

### 6. Deleting unnecessary variables

```{R}
credit = credit %>% dplyr::select(-"...1")
```

### 7. Examining the distribution of our variables

```{R}
ggplot(credit, aes(x = RevolvingUtilizationOfUnsecuredLines)) + geom_histogram()
```

```{r}
credit = credit %>% filter(RevolvingUtilizationOfUnsecuredLines < 2)
ggplot(credit, aes(x = RevolvingUtilizationOfUnsecuredLines)) + geom_histogram()
```

```{r}
ggplot(credit, aes(x = DebtRatio)) + geom_histogram()
```

```{r}
credit = credit %>% filter(DebtRatio < 5)
ggplot(credit, aes(x = DebtRatio)) + geom_histogram()
```

```{r}
ggplot(credit, aes(x = age)) + geom_histogram()
```

```{r}
credit = credit %>% filter(age > 18 & age < 100)
ggplot(credit, aes(x = age)) + geom_histogram()
```

```{r}
ggplot(credit, aes(x = `NumberOfTime30-59DaysPastDueNotWorse`)) + geom_histogram()
```

```{r}
credit = credit %>% filter(`NumberOfTime30-59DaysPastDueNotWorse` < 8)
ggplot(credit, aes(x = `NumberOfTime30-59DaysPastDueNotWorse`)) + geom_histogram()
```

```{R}
ggplot(credit, aes(x = MonthlyIncome)) + geom_histogram()
```

```{r}
credit = credit %>% filter(MonthlyIncome < 50000)
ggplot(credit, aes(x = MonthlyIncome)) + geom_histogram()
```

```{R}
ggplot(credit, aes(x = NumberOfOpenCreditLinesAndLoans)) + geom_histogram()
```

```{R}
credit = credit %>% filter(NumberOfOpenCreditLinesAndLoans < 40)
ggplot(credit, aes(x = NumberOfOpenCreditLinesAndLoans)) + geom_histogram()
```

```{R}
ggplot(credit, aes(x = NumberOfTimes90DaysLate)) + geom_histogram()
```

```{R}
credit = credit %>% filter(NumberOfTimes90DaysLate < 10)
ggplot(credit, aes(x = NumberOfTimes90DaysLate)) + geom_histogram()
```

```{r}
ggplot(credit, aes(x = NumberRealEstateLoansOrLines)) + geom_histogram()
```

```{r}
credit = credit %>% filter(NumberRealEstateLoansOrLines < 10)
ggplot(credit, aes(x = NumberRealEstateLoansOrLines)) + geom_histogram()
```

```{r}
ggplot(credit, aes(x = `NumberOfTime60-89DaysPastDueNotWorse`)) + geom_histogram()
```

```{r}
credit = credit %>% filter(`NumberOfTime60-89DaysPastDueNotWorse` < 10)
ggplot(credit, aes(x = `NumberOfTime60-89DaysPastDueNotWorse`)) + geom_histogram()
```

```{r}
ggplot(credit, aes(x = NumberOfDependents)) + geom_histogram()
```

```{r}
credit = credit %>% filter(NumberOfDependents < 8)
ggplot(credit, aes(x = NumberOfDependents)) + geom_histogram()
```

```{r}
summary(credit)
```

Note: This is a very unbalanced dataset. Look at the number of "Yes" and "No" values in the response variable.

### 8. Splitting our data set

Here we are splitting our data set into train and test sets where I am opting for a 70/30 partition. As always, I am including a seed to ensure my results can be reproduced.

```{r}
set.seed(123) 
train.rows = createDataPartition(y = credit$SeriousDlqin2yrs, p=0.7, list = FALSE) #70% in training
train = slice(credit,train.rows)
test = slice(credit,-train.rows)
```

### 9. Visualizations

```{r}
ggplot(train, aes(x = SeriousDlqin2yrs, y = RevolvingUtilizationOfUnsecuredLines)) + 
  geom_violin() + theme_bw()
```
```{r}
ggplot(train, aes(x = RevolvingUtilizationOfUnsecuredLines)) + 
  geom_histogram() + facet_wrap(~SeriousDlqin2yrs, ncol = 1) + theme_bw()
```

```{r}
ggplot(train, aes(x = SeriousDlqin2yrs, y = age)) + 
  geom_violin() + theme_bw()
```
```{r}
ggplot(train, aes(x = `NumberOfTime30-59DaysPastDueNotWorse`, fill = SeriousDlqin2yrs)) + 
  geom_bar(position = "fill") + theme_bw()
```
```{r}
ggplot(train, aes(x = SeriousDlqin2yrs, y = DebtRatio)) + 
  geom_violin() + theme_bw()
```
```{r}
ggplot(train, aes(x = SeriousDlqin2yrs, y = MonthlyIncome)) + 
  geom_violin() + theme_bw()
```

```{r}
ggplot(train, aes(x = SeriousDlqin2yrs, y = `NumberOfOpenCreditLinesAndLoans`)) + 
  geom_violin() + theme_bw()
```
```{r}
ggplot(train, aes(x = `NumberOfTimes90DaysLate`, fill = SeriousDlqin2yrs)) + 
  geom_bar(position = "fill") + theme_bw()
```
```{r}
ggplot(train, aes(x = `NumberRealEstateLoansOrLines`, fill = SeriousDlqin2yrs)) + 
  geom_bar(position = "fill") + theme_bw()
```
```{r}
ggplot(train, aes(x = `NumberOfTime60-89DaysPastDueNotWorse`, fill = SeriousDlqin2yrs)) + 
  geom_bar(position = "fill") + theme_bw()
```

```{r}
ggplot(train, aes(x = `NumberOfDependents`, fill = SeriousDlqin2yrs)) + 
  geom_bar(position = "fill") + theme_bw()

### 10. Creating our naive model

This step allows us to ensure the predictive power of our models are in fact of use to us.

```{r}
table(train$SeriousDlqin2yrs)
```
```{r}
76202/nrow(train)
```

### 10. Creating our naive model

This step allows us to ensure the predictive power of our models are in fact of use to us.

```{r}
table(train$SeriousDlqin2yrs)
```
```{r}
76202/nrow(train)
```

### 10. Creating our naive model

This step allows us to ensure the predictive power of our models are in fact of use to us.

```{r}
table(train$SeriousDlqin2yrs)
```
```{r}
76202/nrow(train)
```

### 10. Creating our naive model

This step allows us to ensure the predictive power of our models are in fact of use to us.

```{r}
table(train$SeriousDlqin2yrs)
```
```{r}
76202/nrow(train)
```

### 11. Logistic regression

For the sake of time, let's look at what we get from stepwise.
```{r}
allmod = glm(SeriousDlqin2yrs ~., train, family = "binomial") 
summary(allmod)  
  
emptymod = glm(SeriousDlqin2yrs ~1, train, family = "binomial") #use ~1 to build an empty model  
summary(emptymod)
```

Backward stepwise (reusing code from linear regression) 
```{r}
#backward
backmod = stepAIC(allmod, direction = "backward", trace = FALSE) 
summary(backmod)
```

NOTE: Stepwise won't work if you are missing data
Forward stepwise (again reusing code)
```{r}
#forward
forwardmod = stepAIC(emptymod, direction = "forward", scope=list(upper=allmod,lower=emptymod),
                      trace = FALSE) 
summary(forwardmod) 
```

Make predictions to use for threshold selection  
```{r}
predictions = predict(forwardmod, type = "response")
```

Threshold selection via ROC curve  
```{r}
ROCRpred = prediction(predictions, train$SeriousDlqin2yrs) 

ROCRperf = performance(ROCRpred, "tpr", "fpr")
plot(ROCRperf, colorize=TRUE, print.cutoffs.at=seq(0,1,by=0.1), text.adj=c(-0.2,1.7))
```

```{r}
#Determine threshold to balance sensitivity and specificity
opt.cut = function(perf, pred){
    cut.ind = mapply(FUN=function(x, y, p){
        d = (x - 0)^2 + (y-1)^2
        ind = which(d == min(d))
        c(sensitivity = y[[ind]], specificity = 1-x[[ind]], 
            cutoff = p[[ind]])
    }, perf@x.values, perf@y.values, pred@cutoffs)
}
print(opt.cut(ROCRperf, ROCRpred))
```

We can also explicitly look at accuracy by cutoff. 
```{r}
acc.perf = performance(ROCRpred, measure = "acc")
plot(acc.perf)
```

To find the exact value of the cutoff to maximize accuracy:  
```{r}
ind = which.max( slot(acc.perf, "y.values")[[1]] )
acc = slot(acc.perf, "y.values")[[1]][ind]
cutoff = slot(acc.perf, "x.values")[[1]][ind]
print(c(accuracy= acc, cutoff = cutoff))
```

```{r}
#confusion matrix
t1 = table(train$SeriousDlqin2yrs, predictions > 0.4525818)
t1
```
```{r}
(t1[1,1]+t1[2,2])/nrow(train)
```

On the testing set
```{r}
predictions_test = predict(forwardmod, newdata = test, type = "response") #develop predicted probabilities on the training set
```

```{r}
t1 = table(test$SeriousDlqin2yrs,predictions_test > 0.4525818)
t1
```

Calculate accuracy  
```{r}
(t1[1,1]+t1[2,2])/nrow(test)
```

### 11. Classification trees
Set-up folds
```{r}
set.seed(123)
ctrl <- trainControl(method = "cv",
                     number = 5)
```

```{r}
fit1 <- train(x=as.data.frame(train[,-1]), y=train$SeriousDlqin2yrs, 
               method = "rpart",
               trControl = ctrl)
```

```{r}
fit1
fancyRpartPlot(fit1$finalModel) #plot tree (note different syntax when using caret!)
```

Predictions on training set
```{r}
predtrain1 = predict(fit1, train, type = "raw") #note different syntax for caret
```

Examine the confusion matrix
```{r}
confusionMatrix(predtrain1, train$SeriousDlqin2yrs, positive= "Yes")
#Note the positive = MUST be changed to whatever you renamed your positive class of the 
#response variable to be!!!!
```

Predictions on test set
```{r}
predtest1 = predict(fit1, test, type = "raw")
```

Examine the confusion matrix
```{r}
confusionMatrix(predtest1, test$SeriousDlqin2yrs, positive= "Yes")
```

From here: 

* Could try to tune cp  
* Could change performance measure to ROC from accuracy  
* Could add weighting  
* Could stop here and call it "good"

### 12. Random Forests

Letting R tune the model  
```{r}
start_time = Sys.time()

set.seed(999)
rf1 = train(x=as.data.frame(train[,-1]),y=train$SeriousDlqin2yrs,
      method = "ranger",
      importance = "permutation", #necessary for ranger
      trControl = ctrl) #same cross-validation set-up from before

end_time = Sys.time()
end_time - start_time
```

Saving the model (so we don't have to run it again)  
```{r}
saveRDS(rf1, "rf1.rds")
```

Read the model in  
```{r}
rf1 = readRDS("rf1.rds")
```

Examine model and important variables
```{r}
print(rf1)
plot(rf1)
varImp(rf1)
```

Training predictions  
```{R}
trainpredrf1 = predict(rf1, type = "raw") 
confusionMatrix(trainpredrf1,train$SeriousDlqin2yrs,
                positive = "Yes")
```

Test predictions  
```{R}
testpredrf1 = predict(rf1, newdata = test, type="raw")
confusionMatrix(testpredrf1,test$SeriousDlqin2yrs,
                positive = "Yes")
```
