---
layout: post
title: 아동 청소년 자살 예방 제안서
date: 2022-12-09 17:39:00
description: 아동 청소년 자살률 0% 달성을 위한 치료 및 예방 계획
comment : true
---

# **대한민국 아동청소년 자살률 0% 달성을 위한 실현 가능한 계획**
## **Intro**
![suicidal ideation](https://user-images.githubusercontent.com/114209766/206896282-e179b483-b07d-48f9-9a36-1daf77441db7.jpg)
<br/>
대한민국 청소년 자살률은 OCED 국가 중에서도 상당히 높은 수준이다. 2016년 사망원인 통계에 따르면 청소년 사망자 중 약 30%가 극단적 선택으로 세상을 떠났다(이승호, 2018). 우리가 마주하는 자살은 ‘이미 발생한 결과’에 그치겠지만, 당사자가 경험하는 자살은 하나의 ‘과정’이다. Thompson과 동료들(2012)에 따르면, 자살은 “death wish – Ideation – Plan – Attempt” 네 단계를 따라 이루어진다. 우리의 목표는, 이 네 단계 중 하나에 해당하는 아동 청소년을 발견해내고, 그들이 자살에 이르기 전에 예방하는 것이다. 아동 청소년 자살 행동의 특성을 살펴보면, 10세부터 12세까지는 완만히 증가하지만 12세부터 20세까지는 급격히 증가한다(Voss et al., 2019). 즉, 12세부터 20세 사이에 급증하는 자살률에 브레이크를 달아줄 필요가 있을 것으로 보인다. 따라서 우리는, 12세부터 20세(중학교 1학년 ~ 고등학교 3학년)에 해당하는 아동 청소년들 중 자살 과정상에 놓인 아이들을 탐지해내고, 그에 해당하는 치료를 제공할 것을 제안한다. <br/>
또한, 뇌데사씨가 제안한 “아동 청소년 자살률 0%”라는 목표 달성을 위해서는, 전수조사가 필수적일 것으로 예상한다. 사각지대 없이, 모든 아동 청소년에 대해 예방을 위한 손길이 제공되어야만 비로소 자살 사고(suicidal ideation)가 있는 아동 청소년을 모두 탐지해낼 수 있을 것이다. <br/>
이러한 배경을 토대로 보았을 때, S Consulting Inc.에서는 치료와 예방, 두 부분으로 구분하여 접근할 것을 제안한다. 먼저, 자살 행동이 급격히 증가하는 12세부터 20세에 대해서는 자살 위험이 있는 아동 청소년을 탐지해내고 “치료”를 제공할 것이다. 두 번째로, 자살 행동이 완만히 증가하는 10세부터 12세까지는 자살 행동으로 이어지기 전, 미리 스크리닝하여 "예방"해야 한다. <br/>
<br/>
## **Data Analysis for Predicting Suicidal Ideation**
앞에서 제안한 치료와 예방을 실현하기 위해서는 자살 사고가 있는 아동 청소년을 탐지해내는 것이 선행되어야 한다. 따라서, 우리는 **머신 러닝(Machine Learning, ML)** 기법을 통해 자살 사고를 유의미하게 예측하는 요인들이 무엇인지 알아내고자 하였다. 머신 러닝을 위해 ABCD(Adolescent Brain and Cognitive Development) study에서 제공하는 데이터를 활용하였다. ABCD란, 뇌 발달과 아동 건강 증진을 위해 미국에서 수행하고 있는 대형 종단연구 프로젝트이다. 9-10세에 해당하는 약 11,880명의 아이들을 대상으로 초기 성인기에 이르기 전까지 종단적으로 뇌 영상 데이터 등을 수집하고 있으며, 해당 데이터는 다양한 연구 분야에도 활용되고 있다. <br/>
#### **Material : ABCD data**
ABCD에서 도출한 데이터는 크게 fMRI, sMRI, dMRI, PRS, CBCL, sociodemographic variables로 구성된다. <br/>
**fMRI(functional MRI)**는 기능적 자기공명영상으로, 자성을 통해 혈류 변화를 관찰함으로써 뇌의 활성을 간접적으로 관찰하는 brain imaging 기법이다. <br/>
**sMRI(Structural MRI)**는 구조적 MRI로써 뇌의 모양, 크기, 부피, 두께 등의 정보를 포함한다. <br/>
**dMRI(Diffusion MRI)**는 뇌 구조 간의 백질 연결성을 확인할 수 있는 brain imaging 기법이다. <br/>
**PRS(Polygenic Risk Score)**란, 유전적인 ‘위험점수’의 개념이다. 인간의 유전자는 99.9%는 동일하고 약 0.1%에서만 차이가 나타나는데, 사람들 사이에 서로 다르게 나타나는 유전자를 “SNP”이라고 한다. 이때, 특정 질병과 관련된 위험 SNP를 많이 가진 경우, PRS가 높아진다. <br/>
**CBCL(Child Behavior Checklist)**이란, 아동 청소년의 정서, 행동 등을 평가하는 자기보고 척도이다. <br/>
**Sociodemographic variables**에서는 피험자의 성별, 나이, 가정의 수입, 인종, 기혼 여부 등 사회·인구 통계학적 정보를 담고 있다. <br/>
<br/>
가장 먼저, ABCD의 모든 데이터를 활용한 머신 러닝을 수행하여, 자살 사고가 있는 아동을 예측하는 데 중요한 변인이 무엇인지에 대해 대략적으로 파악하고자 하였다. 다음으로, 이전 단계에서 파악한 변인을 중심으로 새로운 변인을 추가하고 삭제하면서 최적의 모델을 찾고자 하였다. <br/>
<br/>
### **Data Preprocessing**
본격적인 머신 러닝을 수행하기에 앞서, 데이터의 전처리가 수행되었다. <br/>
#### **Merge Data**
```
data_list <- list(abcd_SI, abcd_PRS, abcd_cbcl, abcd_brain_gm, abcd_brain_wm, abcd_brain_task, abcd_demographics)
data_inner_join <- join_all(data_list, by = 'subjectkey', type = 'inner')
```
전체 데이터(n = 11879)에서 모든 데이터에 포함된 subject를 inner join 방식으로 merge 했을 때의 데이터는 총 6362개였다. <br/>
#### **NA Imputation**
```
data_inner_join_imp <- data_inner_join %>%
  mutate_if(is.numeric, function(x) ifelse(is.na(x), median(x, na.rm = T), x))

vis_miss(abcd_cbcl, warn_large_data = FALSE)
data_inner_join <- subset(data_inner_join, select=-cbcl_scr_07_stress_nm_2)
```
다음으로, merged data 중 NA 값을 포함한 numeric column에 대해서는 median 값으로 imputation을 진행하였다. 또한, column 전체가 NA 값인 경우, 해당 column을 삭제하였다. <br/>
#### **Filtering & Scaling Data**
```
filtered_col <- nearZeroVar(data_inner_join_imp, saveMetrics = TRUE) %>%
  rownames_to_column() %>%

data_inner_join_imp_filt <- data_inner_join_imp[,-nearZeroVar(data_inner_join_imp)]
```
이후 zero 또는 near-zero variance column을 삭제하는 filtering을 수행하였다. <br/>
```
for(i in 1:ncol(data_inner_join_imp_filt)){
  if(is.numeric(data_inner_join_imp_filt[,i]))
    if(9<i && i!=4015 && i!=4016 && i!=4019 && i!=4020){
    data_inner_join_imp_filt[,i] <- scale(data_inner_join_imp_filt[,i])
  }
}
```
네 번째, numeric data에 대해 평균을 1, 표준편차를 0으로 만들기 위해 데이터를 scaling 하였다. 이때, PRS 변수의 경우 이미 scaling 된 데이터이기 때문에 해당 과정에서는 제외하였다. <br/>
#### **Dummy Coding**
```
data_inner_join_imp_filt <- dummy_cols(.data = data_inner_join_imp_filt,
           select_columns = c("sex.1", "race.ethnicity", "married", "abcd_site"),
           remove_first_dummy = FALSE
)

data_final <- data_inner_join_imp_filt
```
Sociodemographic variable 중 factor type 변인에 대해서는 dummy coding을 수행하였다. 그 결과, “성별, 인종, 기혼 여부, ABCD 데이터가 수집된 장소 정보”가 dummy coding 되었다. <br/>
### **Data Analysis**
#### **Data Split**
```
set.seed(1)

train.index <- createDataPartition(data_final$HCvsSI, p = .7, list = FALSE)
train <- data_final[ train.index,]
test  <- data_final[-train.index,]
```
전처리를 완료한 데이터(n = 1768)는 data split 과정을 통해 train set과 test set으로 나누었다. 이때, test 또는 train set에 특정 데이터가 쏠리는 것을 방지하기 위해, 자살 사고가 있는 사람과 없는 사람의 비율을 train set과 test set에 동일하게 배분하였다(p = .7). 이때 train & test set으로 나누는 것은, test set을 대상으로 머신 러닝을 수행하고 이때 도출된 모델을 기반으로 test set에 적용해보기 위함이다. 모델을 도출해낼 때 사용되지 않은 데이터에 대해 모델을 적용하고 예측해봄으로써 “일반화(generalization)”할 수 있게 된다. <br/>
![ML](https://user-images.githubusercontent.com/114209766/206893862-f10a24c5-d165-49b1-b1a4-ab252516a9d4.jpg)
<br/>

#### **Feature Selection**
```
y <- "HCvsSI"
x <- setdiff(names(train), y)

train[, y] <- as.factor(train[, y])
test[, y] <- as.factor(test[, y])

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
다음으로, 자살 사고를 유의미하게 예측할 수 있는 변인을 추려내기 위해 feature selection을 수행하였다. 유의미하게 나온 변인들을 종합하면 다음과 같다. <br/>

![significant](https://user-images.githubusercontent.com/114209766/206894557-6bdf447d-415b-431f-b201-31b2cef6f8a7.png){: width="100%" height="100%"}
<br/>
#### **Auto ML**
유의미한 변인들을 기반으로, train set에 대해 본격적인 머신 러닝을 수행하였다. 이때, 5-fold 기법을 활용하여 cross-validation이 이루어졌다. 머신 러닝에는 R의 `h2o package`가 활용되었다. <br/>
fMRI, sMRI, dMRI, PRS, CBCL, sociodemographic variable을 모두 포함했을 때, leader model인 GBM에서 자살 사고를 예측하는 변수들의 상대적 중요도는 다음과 같다. <br/>

![shap](https://user-images.githubusercontent.com/114209766/206894619-d6e5bfb4-b2cc-4bf6-a83d-a7014542fe03.png)
<br/>
![last_significant](https://user-images.githubusercontent.com/114209766/206894663-fa10a534-a5eb-4af2-8f64-a100f3b17e9d.png)
<br/>

결과적으로 유의미한 변인을 정리하면 다음과 같다. White matter 주요 변인을 살펴보면, emotion과 관련된 뇌 영역(ex. amygdala, ACC...)이나 보상 체계와 관련된 뇌 영역(ex. insula...) 등의 연결성이 자살 사고를 유의미하게 예측함을 알 수 있다. 특히, amygdala의 높은 연결성이 자살 사고를 유의미하게 예측하는 것은, 자살 사고를 하는 아동 청소년들이 감정 변화에 더욱 민감하기 때문일 수 있음을 시사한다. <br/>
주요 변인 상위 항목 중 가장 많은 비중을 차지하는 것은 CBCL이며, 이는 자기보고 데이터가 자살 사고를 예측하는 데 상당히 유의미하다는 것을 의미한다. 이에 따라, CBCL 변인을 단독으로 활용했을 때 자살 사고 예측력을 확인해보았다. 지금까지 수행한 것과 동일한 과정을 통해 CBCL 단독 예측력을 확인한 결과, AUC가 0.96으로 상당히 높음을 확인하였다. 이는, PRS(AUC = 0.59), sociodemographic(AUC = 0.64), fMRI(AUC = 0.55), sMRI(AUC = 0.55), dMRI(AUC = 0.73) 각각의 단독 예측에 비해 유의미하게 높은 수치이다. <br/>
본 결과에 착안하여, CBCL을 중심으로 한 최적의 모델을 찾고자 하였다. 이때, 최적의 모델로 판단하기 위해 사용한 기준은 **AUC**와 **민감도(sensitivity)**이다. AUC의 경우, 전반적인 모델의 예측력의 척도로 활용하였으며, 민감도는 true positive, 즉 “실제 자살 사고가 있는 아동을 그러하다고 예측할 확률”을 파악하기 위해 활용되었다. 궁극적으로 우리가 목표로 하는 것은 “자살 사고가 있는 아동을 보다 정확히 판별”하는 것이기 때문에 민감도가 모델 성능 판단에 특히 중요한 요인이 되어야 한다고 인식했으며, 결과적으로 특이도(specificity)는 대부분의 모델에서 비슷한 수준을 보였기 때문에 판단 기준에서 제외하였다. <br/>
가장 먼저 추가한 변인들은 *dMRI, PRS, sMRI, sociodemographic* 이다. CBCL에 각각의 변인을 추가해보았을 때, PRS(AUC = 0.95, 민감도 = 0.84), sMRI(AUC = 0.95, 민감도 = 0.82), sociodemographic(AUC = 0.96, 민감도 = 0.85)에 비해 dMRI가 더 좋은 예측력을 보였다(AUC = 0.96, 민감도 = 0.87). 따라서, 본 단계에서는 **“CBCL+dMRI”** 모델을 채택하였다. <br/>
다음으로, 위 모델에 새로운 변인 PRS와 sociodemographic을 다시 추가해보았다. 이때, sociodemographic(AUC = 0.96, 민감도 = 0.86)에 비해 PRS 변인을 추가했을 때 더욱 좋은 성능을 보였다(AUC = 0.96, 민감도 = 0.88). 따라서, 본 단계를 통해 **“CBCL+dMRI+PRS”** 가 가장 최적의 모델로 선정되었다. <br/>
위 모델에서 sociodemographic 변인을 추가하여 예측력을 살펴보았으나, AUC = 0.96, 민감도 = 0.83으로 이전의 모델에 비해 오히려 낮은 민감도를 보였기에 기각되었다. 따라서, 전반적인 예측력과 민감도가 가장 높은 최적의 모델로 “CBCL+dMRI+PRS”가 선정되었다. <br/>
<br/>
## **치료 및 예방 방안 제안**
#### **치료 방안**
지금까지 살펴본 머신 러닝 결과에 따라 모든 아이들을 대상으로 유전자 검사를 실시하고 매년 CBCL, dMRI을 시행한다면, 보다 정확히 자살 사고가 있는 아동 청소년을 예측해낼 수 있을 것이다. 다만, 현실적으로 시간과 비용에 제약이 있으며, 현재 자살 예방을 위한 모든 근거 기반 치료가 인지행동치료(CBT)라는 점에서, CBCL을 제외한 데이터가 치료에 얼마나 도움이 될지 알 수 없다. 다시 말해, 자살률 0% 달성을 위해 “전수조사”가 default가 되어야 하는 상황에서, 모든 검사를 시행했을 때 추가적으로 발생하는 막대한 비용에 비해 예측 정확도의 차이는 크게 유의미하지 않다. 따라서, 당사는 CBCL만 활용하여 매년 전수조사가 가장 합리적일 것으로 판단하였다. CBCL을 통해 고위험군을 예측해낸 후, 자살 생각이 있는 고위험군 아동 청소년에 대해 전문 상담사를 통한 정기적 심리치료, CBT를 시행할 것을 제안한다. <br/>
<br/>
#### **예방 방안**
예방 방안으로는 크게 두 가지를 제안하고자 한다. <br/>
##### **1) CBCL for friends 개발**
첫 번째로, “CBCL for friends” 개발이다. 현재 CBCL은 6~18세 자기보고용(YSR), 부모용(CBCL), 교사용(TRF)로 구성된다. 그러나, 청소년의 변화를 가장 잘 파악하는 것은 또래 친구이다(이승호, 2018). 따라서, 가까운 친구들을 대상으로 서로의 정서, 행동에 대해 평정하는 “CBCL for friends”를 개발하여 부모용, 교사용 설문을 보완할 수 있을 것이라 기대한다. 아동 청소년의 경우, 가정에서 부모와 함께 있을 때, 학교에서 교사와 있을 때, 그리고 학교에서 또래 친구들과 어울릴 때 서로 다른 모습을 보이는 특성이 있기 때문에, 특정 상황에서의 모습만으로는 아동을 오롯이 바라보기 어렵다. 따라서, 현재 존재하는 부모용, 교사용 외에도, 아동 청소년이 가장 오랜 시간을 함께 보낸다고 할 수 있는 “또래 친구”가 관찰하고 응답하는 CBCL for friends가 자살 위험 아동을 사전에 예방하는 데 효과적일 것이라 예상한다. 뿐만 아니라, CBCL for friends를 위해 평정하는 친구의 상태에 관심을 기울이게 함으로써 friend support로 이어질 것이라 기대한다. Friend support는 자살 사고, 시도의 보호 요인, 초기 성인기 우울이나 자살의 보호 요인 등으로 작용할 수 있다(Park & Kim, 2012; Wang et al., 2021). <br/>
<br/>
##### **2) 한국형 바이오뱅크 구축**
두 번째로, ABCD의 사례와 같이 자살 예방을 목적으로 하는 developmental biobank를 구축할 것을 제안한다. 바이오뱅크는 대규모 데이터를 제공함으로써 연구자들로 하여금 새로운 과학적 사실이나 치료적 의의를 발견할 수 있도록 한다. 특히 자살은 문화적인 현상으로, 당사가 자살 사고가 있는 아동 청소년을 예측하기 위해 사용한 데이터는 미국의 ABCD study에서 도출된 것이기에 근본적으로 한국의 아동 청소년 자살을 명확히 예측하는 데엔 한계가 있을 것으로 예상된다. 따라서, 한국형 바이오뱅크를 구축하는 것이 한국 아동 청소년의 자살 사고를 예측하고, 예방하기 위한 가장 효율적인 방법일 것이다. <br/>
<br/>
#### **치료 및 예방을 위해 필요한 예산**
지금까지 제시한 치료와 예방 방안을 실현하기 위해 필요한 예산은 다음과 같다.
##### **치료 예산**
<br/>
![treatment](https://user-images.githubusercontent.com/114209766/206894680-59896570-be26-49c6-b9c2-a7680fbf194d.png)
<br/>
##### **예방 예산**
<br/>
![prevent](https://user-images.githubusercontent.com/114209766/206894688-d00aec0c-886d-4c14-ab93-bd523bf7abe5.png)
<br/>
