---
layout: page
permalink: /codes/
title: codes
description: ML에 사용한 R codes 정리
nav: true
---

# Data Preprocessing
<br/>
## Install & Run Needed Packages
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

### Remove observations with SI = 2 / Remove "HCvsSA" column
abcd_SI <- subset(abcd_SI, abcd_SI$HCvsSI < 2) <br/>
abcd_SI <- abcd_SI %>% select(-HCvsSA) <br/>
<br/>

### Remove CBCL's column except t-score / Remove "eventname" column
abcd_cbcl <- abcd_cbcl %>% select(-ends_with(c('r', 'm', 'nm'))) <br/>
abcd_cbcl <- abcd_cbcl %>% select(-eventname) <br/>
<br/>

## Data Merging
### Check subjectkey format
abcd_cbcl$subjectkey[1] <br/>
abcd_brain_wm$subjectkey[1] <br/>
abcd_SI$subjectkey[1] <br/>
abcd_PRS$subjectkey[1] <br/>
abcd_brain_gm$subjectkey[1] <br/>
abcd_brain_task$subjectkey[1] <br/>
abcd_demographics$subjectkey[1] <br/>
<br/>

### Unify the subjectkey format
abcd_brain_wm <- abcd_brain_wm %>%
  mutate(subjectkey = paste('NDAR','_', substr(subjectkey, 5, 15), sep = '')) <br/>
abcd_SI_Removed <- abcd_SI_Removed %>%
  mutate(subjectkey = paste('NDAR','_', substr(subjectkey, 5, 15), sep = '')) <br/>
abcd_demographics <- abcd_demographics %>%
  mutate(subjectkey = paste('NDAR','_', substr(subjectkey, 5, 15), sep = '')) <br/>

### Merge Data
data_list <- list(abcd_SI, abcd_PRS, abcd_cbcl, abcd_brain_gm, abcd_brain_wm, abcd_brain_task, abcd_demographics) <br/>
data_inner_join <- join_all(data_list, by = 'subjectkey', type = 'inner') <br/>
