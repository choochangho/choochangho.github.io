---
layout: post
title:  "혼동행렬 정리"
categories: 머신러닝 통계학
date:   2016-12-29 08:00
permalink: /archivers/confusion-matrix2
---

| 구분                    |   Predicted Positive   |  Predicted Negative  |
|------------------------|------------------------|----------------------|
| Observed Positive      |          TP            |         FN           |
| Observed Negative      |          FP            |         TN           |

### Error rate, ERR

- the number of all incorrect predictions / the total number of the dataset
- BEST : 0.0, WORST : 1.0

$$
\begin{align}
\text{ERR} &= \frac{FP + FN}{TP + FP + TN + FN} \\
&= \frac{FP + FN}{P + N}

\end{align}
$$



### Accuracy(ACC), 정확도

- the number of correct predictions / the total number of datasets
- MAX: 1, MIN: 0
- = ERR - 1

$$
\begin{align}
\text{ACC} &= \frac{TP + TN}{TP + FP + TN + FN} \\
&= \frac{TP + TN}{P + N}

\end{align}
$$

### Sensitivity(SN), 민감도, Recall, True Positive Rate(TPR)

- the number of correct positive predictions / the total number of positives.
- BEST: 1.0, WORST: 0.0

$$
\begin{align}
\text{SN} &= \frac{TP}{TP + FN} \\
&= \frac{TP}{P}

\end{align}
$$

### Specificity(SP), 특이도, True Negative Rate(TNR)

- the number of correct negative predictions / the total number of negatives.
- BEST: 1.0, WORST: 0.0

$$
\begin{align}
\text{SP} &= \frac{TN}{FP + TN} \\
&= \frac{TN}{N}

\end{align}
$$

### Precision(PREC), 정밀도, Positive predictive value(PPV)

- the number of correct positive predictions / the total number of positive predictions

$$
\begin{align}
\text{PREC} &= \frac{TP}{TP + FP}

\end{align}
$$

### False Positive Rate(FPR)

- the number of incorrect negative predictions / the total number of negatives

$$
\begin{align}
\text{PREC} &= \frac{FP}{FP + TN} \\
&= 1- SP

\end{align}
$$

## Correlation coefficient and F-score

### Matthews correlation coefficient

$$
\begin{align}
\text{MCC} = \frac{TP \times TN - FP \times FN}{\sqrt{(TP + FP)(TP + FN)(TN + FP)(TN + FN)}}
\end{align}
$$

### F-score

harmonic mean of precision(PREC) and recall(REC)

$$
\begin{align}
F_{\beta} = \frac{(1 + \beta^2)(PREC \times REC)}{(\beta^2 \times PREC + REC)}
\end{align}
$$

$$ \beta $$ is commonly 0.5, 1, or 2.

$$
\begin{align}
F_{0.5} = \frac{1.25 \times PREC \times REC}{0.25 \times PREC + REC}
\end{align}
$$

$$
\begin{align}
F_{1} = \frac{2 \times PREC \times REC}{PREC + REC}
\end{align}
$$

$$
\begin{align}
F_{2} = \frac{5 \times PREC \times REC}{4 \times PREC + REC}
\end{align}
$$