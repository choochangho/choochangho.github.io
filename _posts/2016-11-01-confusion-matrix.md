---
layout: post
title:  "혼동행렬 정리"
categories: 머신러닝 통계학
date:   2016-11-01 09:00
permalink: /archivers/confusion-matrix
---

# 혼동행렬(Confusion matrix)

## 정의

알고리즘의 성능을 평가할 때, 평가하는 지표로 사용된다.

## 질병발생 예측 데이터

| n=165                  | 질병이 아니라고 예측 **NO** | 질병이라고 예측 **YES**  |
|------------------------|------------------------|----------------------|
| 검사결과 질병이 아님 **NO** |          50            |         10           |
| 검사결과 질병임 **YES**    |           5            |         100          |

- 에측범주는 두 가지가 가능 : YES(질병에 걸림), NO(질병에 걸리지 않음)
- 총 165 예측 데이터가 존재 : n = 165
- 총 165 예측 데이터중 YES 로 예측이 110건, NO 로 예측이 55 건
- 실제로는 105명의 환자가 질병에 걸림.

## 용어정리

- **true positives(TP)**: YES 로 예측하고 질병으로 진단(YES) 받은 경우.
- **true negatives(TN)**: NO 로 예측하고 질병이 아니라고 진단(NO) 받은 경우.
- **false positives(FP)**: YES 로 예측하고 질병이 아니라고 진단(NO) 받은 경우(Type I error).
- **false negatives(FN)**: NO 로 예측하고 질병이 아니라고 진단(NO) 받은 경우(Type II error).

다시 표로 정리하자면...

| n=165                  | 질병이 아니라고 예측 **NO** | 질병이라고 예측 **YES**  |  Total|
|------------------------|------------------------|----------------------|------|
| 검사결과 질병이 아님 **NO** |         **TN**=50          |      **FP**=10           |  60  |
| 검사결과 질병임 **YES**    |         **FN**=5           |       **TP**=100         | 105  |
|  Total                 |           55           |          110          |     |


## 이진분류(binary classification) 에서의 각종 지표

- Accuracy: 얼마나 정확한가?
  - (TP + TN) / total = (100 + 50) / 165 = 0.91
- Misclassification Rate: 오차율
  - (FP + FN) / total = (10 + 5) / 165 = 0.09
  - '1 - 정밀도'( 1- 0.91 = 0.09 ) 와 동일
  - Error Rate 
- 민감도(True Positive Rate): 실제로 질병으로 진단 받은 것중 질병으로 예측된 비중
  - TP / 검사결과 질병 = 100 / 105 = 0.95
  - 'Sensitivity', 'Recall'
- False Positive Rate: 실제로 질병이 아닌것으로 진단 받은 것중 질병이라고 예측된 비중
  - FP / 검사결과 잘병이 아님 = 10 / (50 + 10) = 0.17
- 특이도(Specificity): 실제로 질병이 아닌것으로 진단 받은 것중 질병이 아니라고 예측된 비중
  - TN / (50 + 10) = 50 / 60 = 0.83
  - 1 - False Positive Rate = 1 - 0.17 = 0.83
- Precision: 질병이라고 예측한 것이 얼마나 정확한가?
  - TP / (FP + TP) = 100 / 110 = 0.91
- Prevalence : 질병이라고 진단 받은 비중
  - (FN + TP) / 165 = 105 / 165 = 0.64

- Positive Predictive Value(PPV) 
\begin{align}\frac{\text{number of true positive}}{\text{number of true positive} + \text{number of false positive}} = \frac{100}{100 + 10} = 0.91 = Precision\end{align}