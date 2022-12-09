---
layout: page
permalink: /codes/
title: codes
description: ML에 사용한 R codes 정리
nav: true
---

# **Data Preprocessing**
## Install & Run Needed Packages
{% highlight c++ linenos %}
install.packages('plyr') <br/>
install.packages('dplyr') <br/>
install.packages('visdat') <br/>
install.packages('ggplot2') <br/>
install.packages('caret') <br/>
install.packages('tibble') <br/>
install.packages('tidyselect') <br/>
install.packages(fastDummies) <br/>
install.packages(h2o) <br/>
install.packages(kableExtra) <br/>
<br/>
library(plyr) <br/>
library(dplyr) <br/>
library(visdat) <br/>
library(ggplot2) <br/>
library(caret) <br/>
library(tibble) <br/>
library(tidyselect) <br/>
library(fastDummies) <br/>
library(h2o) <br/>
library(kableExtra) <br/>
{% endhighlight %}
<br/>

## Import Data
abcd_SI <- read.csv('abcd_suicidal_ideation_hc0_SI1.csv') <br/>
abcd_PRS <- read.csv('abcd_polygenic_scores.csv') <br/>
abcd_cbcl <- read.csv('abcd_cbcl01.csv') <br/>
abcd_brain_gm <- read.csv('abcd_brain_gm_freesurfer.csv') <br/>
abcd_brain_wm <- read.csv('abcd_brain_wm_connectivity_fa.csv') <br/>
abcd_brain_task <- read.csv('abcd_brain_task-fMRI_StopSignalTest.csv') <br/>
abcd_demographics <- read.csv('abcd_demographics.csv') <br/>
<br/>

#### Remove observations with SI = 2 / Remove "HCvsSA" column / Change "HCvsSI" column as factor
abcd_SI <- subset(abcd_SI, abcd_SI$HCvsSI < 2) <br/>
abcd_SI <- abcd_SI %>% select(-HCvsSA) <br/>
abcd_SI$HCvsSI <- as.factor(abcd_SI$HCvsSI) <br/>
<br/>

#### Remove CBCL's column except t-score / Remove "eventname" column
abcd_cbcl <- abcd_cbcl %>% select(-ends_with(c('r', 'm', 'nm'))) <br/>
abcd_cbcl <- abcd_cbcl %>% select(-eventname) <br/>
<br/>

## Data Merging
#### Check subjectkey format
abcd_cbcl$subjectkey[1] <br/>
abcd_brain_wm$subjectkey[1] <br/>
abcd_SI$subjectkey[1] <br/>
abcd_PRS$subjectkey[1] <br/>
abcd_brain_gm$subjectkey[1] <br/>
abcd_brain_task$subjectkey[1] <br/>
abcd_demographics$subjectkey[1] <br/>
<br/>

#### Unify the subjectkey format
abcd_brain_wm <- abcd_brain_wm %>%
  mutate(subjectkey = paste('NDAR','_', substr(subjectkey, 5, 15), sep = '')) <br/>
abcd_SI <- abcd_SI %>%
  mutate(subjectkey = paste('NDAR','_', substr(subjectkey, 5, 15), sep = '')) <br/>
abcd_demographics <- abcd_demographics %>%
  mutate(subjectkey = paste('NDAR','_', substr(subjectkey, 5, 15), sep = '')) <br/>
<br/>

#### Merge Data
data_list <- list(abcd_SI, abcd_PRS, abcd_cbcl, abcd_brain_gm, abcd_brain_wm, abcd_brain_task, abcd_demographics) <br/>
data_inner_join <- join_all(data_list, by = 'subjectkey', type = 'inner') <br/>
<br/>

## Missing Data
#### Remove empty column
data_inner_join <- subset(data_inner_join, select=-cbcl_scr_07_stress_nm_2) <br/>
<br/>

#### Median NA imputation for numeric variables
data_inner_join_imp <- data_inner_join %>%
  mutate_if(is.numeric, function(x) ifelse(is.na(x), median(x, na.rm = T), x)) <br/>
<br/>

## Data Filtering
#### Filtering out zero or near-zero variance Features
filtered_col <- nearZeroVar(data_inner_join_imp, saveMetrics = TRUE) %>%
  rownames_to_column() %>%
  filter(nzv) <br/>
data_inner_join_imp_filt <- data_inner_join_imp[,-nearZeroVar(data_inner_join_imp)] <br/>
<br/>

## Data Scaling
**- check PRS columns : already scaled** <br/>
names(data_inner_join_imp_filt)[4:10] <br/>
<br/>
**- check demographic columns : "sex", "race.ethnicity", "married", "abcd_site" should be factor** <br/>
names(data_inner_join_imp_filt)[4037:4044] <br/>
<br/>
**- Data scaling except factor columns** <br/>
for(i in 1:ncol(data_inner_join_imp_filt)){
  if(is.numeric(data_inner_join_imp_filt[,i]))
    if(i<4 || 10<i && i!=4038 && i!=4039 && i!=4042 && i!=4043){
    data_inner_join_imp_filt[,i] <- scale(data_inner_join_imp_filt[,i])
  }
} <br/>
<br/>

## Dummy Coding
**- Dummy code categorical variables** <br/>
data_inner_join_imp_filt <- dummy_cols(.data = data_inner_join_imp_filt,
           select_columns = c("sex.1", "race.ethnicity", "married", "abcd_site"),
           remove_first_dummy = FALSE
) <br/>
<br/>

# **Data Analysis**
## Data Split
set.seed(1) <br/>
<br/>
**- Split into train and test data** <br/>
train.index <- createDataPartition(data_final$HCvsSI, p = .7, list = FALSE) <br/>
train <- data_final[ train.index,] <br/>
test  <- data_final[-train.index,] <br/>
<br/>

## Feature Selection
y <- "HCvsSI" <br/>
x <- setdiff(names(train), y) <br/>
<br/>
train[, y] <- as.factor(train[, y]) <br/>
test[, y] <- as.factor(test[, y]) <br/>
<br/>
x_significant <- c()
for (i in x){
  form <- paste(y,'~',i)
  fit <- glm(as.formula(form), family='binomial', data=train)
  p_value <- coef(summary(fit))[,4][2]
  if(p_value < 0.05){
    x_significant <- append(x_significant, names(p_value))
  }
} <br/>
<br/>
**- add age as the necessary covariate** <br/>
x_significant <- append('age', x_significant) <br/>
<br/>
**- extract the significant features from the train and test sets** <br/>
train <- subset(train, select=c(y, x_significant)) <br/>
test <- subset(test, select=c(y, x_significant)) <br/>
<br/>

## Auto ML
h2o.init() <br/>
<br/>
**- create an index column for stratified cross-validation** <br/>
fold_column <- rep(0, nrow(train)) <br/>
train <- data.frame(fold_column, train) <br/>
<br/>
k_folds <- 5 <br/>
set.seed(1) <br/>
train$fold_column <- createFolds(train$HCvsSI, k=k_folds, list=FALSE) <br/>
<br/>
table(train$fold_column[which(train$HCvsSI == 0)]) <br/>
table(train$fold_column[which(train$HCvsSI == 1)]) <br/>
<br/>
**- train the model** <br/>
train_h2o <- as.h2o(train, use_datatable = TRUE) <br/>
<br/>
aml <- h2o.automl(x = x_significant, y = y,
                  training_frame = train_h2o,
                  max_models = 20,
                  seed = 1,
                  fold_column = 'fold_column') <br/>
<br/>

## Prediction
test_h2o <- as.h2o(test, use_datatable = TRUE) <br/>
<br/>
pred <- h2o.predict(aml, test_h2o) # object directly <br/>
head(as.data.frame(pred)) %>%
  kbl() %>%
  kable_styling(bootstrap_options=c('condensed','striped')) <br/>
<br/>
**- check the performance of the leader model** <br/>
perf <- h2o.performance(aml@leader, test_h2o) <br/>
perf <br/>
<br/>

## Explainability
**- explain leader model & compare with all AutoML models** <br/>
exp <- h2o.explain(aml, test_h2o) <br/>
exp <br/>
<br/>
**- explain a single H2O model (e.g. leader model from AutoML)** <br/>
exm <- h2o.explain(aml@leader, test_h2o) <br/>
exm <br/>
