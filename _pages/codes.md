---
layout: page
permalink: /codes/
title: codes
description: ML에 사용한 R codes 정리
nav: true
---

# **Data Preprocessing**
## Install & Run Needed Packages
```
install.packages('plyr')
install.packages('dplyr')
install.packages('visdat')
install.packages('ggplot2')
install.packages('caret')
install.packages('tibble')
install.packages('tidyselect')
install.packages('fastDummies')
install.packages('h2o')
install.packages('kableExtra')

library(plyr)
library(dplyr)
library(visdat)
library(ggplot2)
library(caret)
library(tibble)
library(tidyselect)
library(fastDummies)
library(h2o)
library(kableExtra)
```
<br/>

## Import Data
```
abcd_SI <- read.csv('abcd_suicidal_ideation_hc0_SI1.csv')
abcd_PRS <- read.csv('abcd_polygenic_scores.csv')
abcd_cbcl <- read.csv('abcd_cbcl01.csv')
abcd_brain_gm <- read.csv('abcd_brain_gm_freesurfer.csv')
abcd_brain_wm <- read.csv('abcd_brain_wm_connectivity_fa.csv')
abcd_brain_task <- read.csv('abcd_brain_task-fMRI_StopSignalTest.csv')
abcd_demographics <- read.csv('abcd_demographics.csv')
```
<br/>

#### Remove observations with SI = 2 / Remove "HCvsSA" column / Change "HCvsSI" column as factor
```
abcd_SI <- subset(abcd_SI, abcd_SI$HCvsSI < 2)
abcd_SI <- abcd_SI %>% select(-HCvsSA)
abcd_SI$HCvsSI <- as.factor(abcd_SI$HCvsSI)
```
<br/>

#### Remove CBCL's column except t-score / Remove "eventname" column
```
abcd_cbcl <- abcd_cbcl %>% select(-ends_with(c('r', 'm', 'nm')))
abcd_cbcl <- abcd_cbcl %>% select(-eventname)
```
<br/>

## Data Merging
#### Check subjectkey format
```
abcd_cbcl$subjectkey[1]
abcd_brain_wm$subjectkey[1]
abcd_SI$subjectkey[1]
abcd_PRS$subjectkey[1]
abcd_brain_gm$subjectkey[1]
abcd_brain_task$subjectkey[1]
abcd_demographics$subjectkey[1]
```
<br/>

#### Unify the subjectkey format
```
abcd_brain_wm <- abcd_brain_wm %>%
  mutate(subjectkey = paste('NDAR','_', substr(subjectkey, 5, 15), sep = ''))
abcd_SI <- abcd_SI %>%
  mutate(subjectkey = paste('NDAR','_', substr(subjectkey, 5, 15), sep = ''))
abcd_demographics <- abcd_demographics %>%
  mutate(subjectkey = paste('NDAR','_', substr(subjectkey, 5, 15), sep = ''))
```
<br/>

#### Merge Data
```
data_list <- list(abcd_SI, abcd_PRS, abcd_cbcl, abcd_brain_gm, abcd_brain_wm, abcd_brain_task, abcd_demographics)
data_inner_join <- join_all(data_list, by = 'subjectkey', type = 'inner')
```
#### Single Data Prediction : Add Variable into "data_list"
```
data_list <- list(abcd_SI, abcd_PRS)
data_list <- list(abcd_SI, abcd_cbcl)
data_list <- list(abcd_SI, abcd_brain_gm)
data_list <- list(abcd_SI, abcd_brain_wm)
data_list <- list(abcd_SI, abcd_brain_task)
data_list <- list(abcd_SI, abcd_demographics)
```

## Missing Data
#### Remove empty column
```
data_inner_join <- subset(data_inner_join, select=-cbcl_scr_07_stress_nm_2)
```
<br/>

#### Median NA imputation for numeric variables
```
data_inner_join_imp <- data_inner_join %>%
  mutate_if(is.numeric, function(x) ifelse(is.na(x), median(x, na.rm = T), x))
```
<br/>

## Data Filtering
#### Filtering out zero or near-zero variance Features
```
filtered_col <- nearZeroVar(data_inner_join_imp, saveMetrics = TRUE) %>%
  rownames_to_column() %>%
  filter(nzv) <br/>
data_inner_join_imp_filt <- data_inner_join_imp[,-nearZeroVar(data_inner_join_imp)]
```
<br/>

## Data Scaling
**- check PRS & demographic columns : don't need scaling** <br/>
```
names(data_inner_join_imp_filt)[4:10]
names(data_inner_join_imp_filt)[4037:4044]
```
<br/>
**- Data scaling except factor columns** <br/>
```
for(i in 1:ncol(data_inner_join_imp_filt)){
  if(is.numeric(data_inner_join_imp_filt[,i]))
    if(i<4 || 10<i && i!=4038 && i!=4039 && i!=4042 && i!=4043){
    data_inner_join_imp_filt[,i] <- scale(data_inner_join_imp_filt[,i])
  }
}
```
<br/>

## Dummy Coding
**- Dummy code categorical variables** <br/>
```
data_inner_join_imp_filt <- dummy_cols(.data = data_inner_join_imp_filt,
           select_columns = c("sex.1", "race.ethnicity", "married", "abcd_site"),
           remove_first_dummy = FALSE
)
```
<br/>

# **Data Analysis**
## Data Split
```
set.seed(1)
```
<br/>
**- Split into train and test data** <br/>
```
train.index <- createDataPartition(data_final$HCvsSI, p = .7, list = FALSE)
train <- data_final[ train.index,]
test  <- data_final[-train.index,]
```
<br/>

## Feature Selection
```
y <- "HCvsSI"
x <- setdiff(names(train), y)
```
<br/>
```
train[, y] <- as.factor(train[, y])
test[, y] <- as.factor(test[, y])
```
<br/>
```
x_significant <- c()
for (i in x){
  form <- paste(y,'~',i)
  fit <- glm(as.formula(form), family='binomial', data=train)
  p_value <- coef(summary(fit))[,4][2]
  if(p_value < 0.05){
    x_significant <- append(x_significant, names(p_value))
  }
}
```
<br/>
**- add age as the necessary covariate** <br/>
```
x_significant <- append('age', x_significant)
```
<br/>
**- extract the significant features from the train and test sets** <br/>
```
train <- subset(train, select=c(y, x_significant))
test <- subset(test, select=c(y, x_significant))
```
<br/>

## Auto ML
```
h2o.init()
```
<br/>
**- create an index column for stratified cross-validation** <br/>
```
fold_column <- rep(0, nrow(train))
train <- data.frame(fold_column, train)
```
<br/>
```
k_folds <- 5
set.seed(1)
train$fold_column <- createFolds(train$HCvsSI, k=k_folds, list=FALSE)
```
<br/>
```
table(train$fold_column[which(train$HCvsSI == 0)])
table(train$fold_column[which(train$HCvsSI == 1)])
```
<br/>
**- train the model** <br/>
```
train_h2o <- as.h2o(train, use_datatable = TRUE)
```
<br/>
```
aml <- h2o.automl(x = x_significant, y = y,
                  training_frame = train_h2o,
                  max_models = 20,
                  seed = 1,
                  fold_column = 'fold_column')
```
<br/>

## Prediction
```
test_h2o <- as.h2o(test, use_datatable = TRUE)
```
<br/>
```
pred <- h2o.predict(aml, test_h2o
head(as.data.frame(pred)) %>%
  kbl() %>%
  kable_styling(bootstrap_options=c('condensed','striped'))
```
<br/>
**- check the performance of the leader model** <br/>
```
perf <- h2o.performance(aml@leader, test_h2o)
perf
```
<br/>

## Explainability
**- explain leader model & compare with all AutoML models** <br/>
```
exp <- h2o.explain(aml, test_h2o)
exp
```
<br/>
**- explain a single H2O model (e.g. leader model from AutoML)** <br/>
```
exm <- h2o.explain(aml@leader, test_h2o)
exm
```
