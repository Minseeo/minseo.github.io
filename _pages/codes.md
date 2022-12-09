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
{% endhighlight %}
<br/>

## Import Data
{% highlight c++ linenos %}
abcd_SI <- read.csv('abcd_suicidal_ideation_hc0_SI1.csv')
abcd_PRS <- read.csv('abcd_polygenic_scores.csv')
abcd_cbcl <- read.csv('abcd_cbcl01.csv')
abcd_brain_gm <- read.csv('abcd_brain_gm_freesurfer.csv')
abcd_brain_wm <- read.csv('abcd_brain_wm_connectivity_fa.csv')
abcd_brain_task <- read.csv('abcd_brain_task-fMRI_StopSignalTest.csv')
abcd_demographics <- read.csv('abcd_demographics.csv')
{% endhighlight %}
<br/>

#### Remove observations with SI = 2 / Remove "HCvsSA" column / Change "HCvsSI" column as factor
{% highlight c++ linenos %}
abcd_SI <- subset(abcd_SI, abcd_SI$HCvsSI < 2)
abcd_SI <- abcd_SI %>% select(-HCvsSA)
abcd_SI$HCvsSI <- as.factor(abcd_SI$HCvsSI)
{% endhighlight %}
<br/>

#### Remove CBCL's column except t-score / Remove "eventname" column
{% highlight c++ linenos %}
abcd_cbcl <- abcd_cbcl %>% select(-ends_with(c('r', 'm', 'nm')))
abcd_cbcl <- abcd_cbcl %>% select(-eventname)
{% endhighlight %}
<br/>

## Data Merging
#### Check subjectkey format
{% highlight c++ linenos %}
abcd_cbcl$subjectkey[1]
abcd_brain_wm$subjectkey[1]
abcd_SI$subjectkey[1]
abcd_PRS$subjectkey[1]
abcd_brain_gm$subjectkey[1]
abcd_brain_task$subjectkey[1]
abcd_demographics$subjectkey[1]
{% endhighlight %}
<br/>

#### Unify the subjectkey format
{% highlight c++ linenos %}
abcd_brain_wm <- abcd_brain_wm %>%
  mutate(subjectkey = paste('NDAR','_', substr(subjectkey, 5, 15), sep = ''))
abcd_SI <- abcd_SI %>%
  mutate(subjectkey = paste('NDAR','_', substr(subjectkey, 5, 15), sep = ''))
abcd_demographics <- abcd_demographics %>%
  mutate(subjectkey = paste('NDAR','_', substr(subjectkey, 5, 15), sep = ''))
{% endhighlight %}
<br/>

#### Merge Data
{% highlight c++ linenos %}
data_list <- list(abcd_SI, abcd_PRS, abcd_cbcl, abcd_brain_gm, abcd_brain_wm, abcd_brain_task, abcd_demographics)
data_inner_join <- join_all(data_list, by = 'subjectkey', type = 'inner')
{% endhighlight %}
<br/>

## Missing Data
#### Remove empty column
{% highlight c++ linenos %}
data_inner_join <- subset(data_inner_join, select=-cbcl_scr_07_stress_nm_2)
{% endhighlight %}
<br/>

#### Median NA imputation for numeric variables
{% highlight c++ linenos %}
data_inner_join_imp <- data_inner_join %>%
  mutate_if(is.numeric, function(x) ifelse(is.na(x), median(x, na.rm = T), x))
{% endhighlight %}
<br/>

## Data Filtering
#### Filtering out zero or near-zero variance Features
{% highlight c++ linenos %}
filtered_col <- nearZeroVar(data_inner_join_imp, saveMetrics = TRUE) %>%
  rownames_to_column() %>%
  filter(nzv) <br/>
data_inner_join_imp_filt <- data_inner_join_imp[,-nearZeroVar(data_inner_join_imp)]
{% endhighlight %}
<br/>

## Data Scaling
**- check PRS columns : already scaled** <br/>
{% highlight c++ linenos %}
names(data_inner_join_imp_filt)[4:10]
{% endhighlight %}
<br/>
**- check demographic columns : "sex", "race.ethnicity", "married", "abcd_site" should be factor** <br/>
{% highlight c++ linenos %}
names(data_inner_join_imp_filt)[4037:4044]
{% endhighlight %}
<br/>
**- Data scaling except factor columns** <br/>
{% highlight c++ linenos %}
for(i in 1:ncol(data_inner_join_imp_filt)){
  if(is.numeric(data_inner_join_imp_filt[,i]))
    if(i<4 || 10<i && i!=4038 && i!=4039 && i!=4042 && i!=4043){
    data_inner_join_imp_filt[,i] <- scale(data_inner_join_imp_filt[,i])
  }
}
{% endhighlight %}
<br/>

## Dummy Coding
**- Dummy code categorical variables** <br/>
{% highlight c++ linenos %}
data_inner_join_imp_filt <- dummy_cols(.data = data_inner_join_imp_filt,
           select_columns = c("sex.1", "race.ethnicity", "married", "abcd_site"),
           remove_first_dummy = FALSE
)
{% endhighlight %}
<br/>

# **Data Analysis**
## Data Split
{% highlight c++ linenos %}
set.seed(1)
{% endhighlight %}
<br/>
**- Split into train and test data** <br/>
{% highlight c++ linenos %}
train.index <- createDataPartition(data_final$HCvsSI, p = .7, list = FALSE)
train <- data_final[ train.index,]
test  <- data_final[-train.index,]
{% endhighlight %}
<br/>

## Feature Selection
{% highlight c++ linenos %}
y <- "HCvsSI"
x <- setdiff(names(train), y)
{% endhighlight %}
<br/>
{% highlight c++ linenos %}
train[, y] <- as.factor(train[, y])
test[, y] <- as.factor(test[, y])
{% endhighlight %}
<br/>
{% highlight c++ linenos %}
x_significant <- c()
for (i in x){
  form <- paste(y,'~',i)
  fit <- glm(as.formula(form), family='binomial', data=train)
  p_value <- coef(summary(fit))[,4][2]
  if(p_value < 0.05){
    x_significant <- append(x_significant, names(p_value))
  }
}
{% endhighlight %}
<br/>
**- add age as the necessary covariate** <br/>
{% highlight c++ linenos %}
x_significant <- append('age', x_significant)
{% endhighlight %}
<br/>
**- extract the significant features from the train and test sets** <br/>
{% highlight c++ linenos %}
train <- subset(train, select=c(y, x_significant))
test <- subset(test, select=c(y, x_significant))
{% endhighlight %}
<br/>

## Auto ML
{% highlight c++ linenos %}
h2o.init()
{% endhighlight %}
<br/>
**- create an index column for stratified cross-validation** <br/>
{% highlight c++ linenos %}
fold_column <- rep(0, nrow(train))
train <- data.frame(fold_column, train)
{% endhighlight %}
<br/>
{% highlight c++ linenos %}
k_folds <- 5
set.seed(1)
train$fold_column <- createFolds(train$HCvsSI, k=k_folds, list=FALSE)
{% endhighlight %}
<br/>
{% highlight c++ linenos %}
table(train$fold_column[which(train$HCvsSI == 0)])
table(train$fold_column[which(train$HCvsSI == 1)])
{% endhighlight %}
<br/>
**- train the model** <br/>
{% highlight c++ linenos %}
train_h2o <- as.h2o(train, use_datatable = TRUE)
{% endhighlight %}
<br/>
{% highlight c++ linenos %}
aml <- h2o.automl(x = x_significant, y = y,
                  training_frame = train_h2o,
                  max_models = 20,
                  seed = 1,
                  fold_column = 'fold_column')
{% endhighlight %}
<br/>

## Prediction
{% highlight c++ linenos %}
test_h2o <- as.h2o(test, use_datatable = TRUE)
{% endhighlight %}
<br/>
{% highlight c++ linenos %}
pred <- h2o.predict(aml, test_h2o
head(as.data.frame(pred)) %>%
  kbl() %>%
  kable_styling(bootstrap_options=c('condensed','striped'))
{% endhighlight %}
<br/>
**- check the performance of the leader model** <br/>
{% highlight c++ linenos %}
perf <- h2o.performance(aml@leader, test_h2o)
perf
{% endhighlight %}
<br/>

## Explainability
**- explain leader model & compare with all AutoML models** <br/>
{% highlight c++ linenos %}
exp <- h2o.explain(aml, test_h2o)
exp
{% endhighlight %}
<br/>
**- explain a single H2O model (e.g. leader model from AutoML)** <br/>
{% highlight c++ linenos %}
exm <- h2o.explain(aml@leader, test_h2o)
exm
{% endhighlight %}
