---
layout: post
title:  "IRIS 데이터셋 분석하기"
categories: 머신러닝 통계학
date:   2016-10-31 09:00
permalink: /archivers/iris-dataset
---

[Introduction to machine learning with scikit-learn 정리](https://github.com/justmarkham/scikit-learn-videos)

# 1. 데이터 살펴보기

```python
from IPython.display import HTML
HTML('<iframe src="http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data" witdth="300" height="200"></iframe>')

# import load_iris function from dataset module 
from sklearn.datsets import load_iris
```

iris 데이터 타입 

```python
iris = load_iris()
type(iris)

# sklearn.datasets.base.Bunch
```

iris 데이터 보기

```python
print(iris.data)


> [[ 5.1  3.5  1.4  0.2]
 [ 4.9  3.   1.4  0.2]
 [ 4.7  3.2  1.3  0.2]
 [ 4.6  3.1  1.5  0.2]
 ...
 ...
 ...
 [ 6.5  3.   5.2  2. ]
 [ 6.2  3.4  5.4  2.3]
 [ 5.9  3.   5.1  1.8]]
```

iris 종류(species) 보기 - target

```python
print(iris.target)


> [0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
 0 0 0 0 0 0 0 0 0 0 0 0 0 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 2 2 2 2 2 2 2 2 2 2 2
 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2
 2 2]
```

iris 종류(species) 보기 - target_names

```python
# print the encoding scheme for species: 0 = setosa, 1 = versicolor, 2 = virginica
print(iris.target_names)

> ['setosa' 'versicolor' 'virginica']
```

feature 의 shape 확인하기

```python
print(iris.data.shape)

> (150, 4)
```

response(응답변수)의 shape 확인하기

```python
print(iris.target.shape)

> (150,)
```

feature 와 response 저장

```python
# store feature matrix in "X"
X = iris.data

# store response vector in "y"
y = iris.target
```

# 2.Model Training

## 사용할 모델 임포트

```python
from sklearn.neighbors import KNeighborsClassifier
```

## 모델 생성하기
```python
# set hyperparameters(tuning parameters) to "1"
knn = KNeighborsClassifier(n_neighbors=1)

print(knn)

> KNeighborsClassifier(algorithm='auto', leaf_size=30, metric='minkowski',
           metric_params=None, n_jobs=1, n_neighbors=1, p=2,
           weights='uniform')
```

## Model Training

```python
knn.fit(X, y)

> KNeighborsClassifier(algorithm='auto', leaf_size=30, metric='minkowski',
           metric_params=None, n_jobs=1, n_neighbors=1, p=2,
           weights='uniform')
```

## 새로운 관측데이터로 응답변수 예측하기

```python
#print(iris.traget_names)
knn.predict([3, 5, 4, 2])

# expected species is 'virginica'
> array([2])

X_new = [[3, 5, 4, 2], [5, 4, 3, 2]]
knn.predict(X_new)

# 'virginica', 'versicolor'
> array([2, 1])
```

## 다른 tuning parameters(K=5) 사용하기

```python
# instantiate the model (using the value K=5)
knn = KNeighborsClassifier(n_neighbors=5)

# fit the model with data
knn.fit(X, y)

# predict the response for new observations
knn.predict(X_new)

# 'versicolor', 'versicolor'
> array([1, 1])
```

## 다른 모델로 사용하기

```python
# import the class
from sklearn.linear_model import LogisticRegression

# instantiate the model (using the default parameters)
logreg = LogisticRegression()

# fit the model with data
logreg.fit(X, y)

# predict the response for new observations
logreg.predict(X_new)

# 'virginica', 'setosa'
> array([2, 0])
```

# 3.scikit-learn 에서 모델 비교

## Logistic regression

```python
# import the class
from sklearn.linear_model import LogisticRegression

# instantiate the model (using the default parameters)
logreg = LogisticRegression()

# fit the model with data
logreg.fit(X, y)

# predict the response values for the observations in X
logreg.predict(X)

# store the predicted response values
y_pred = logreg.predict(X)

# check how many predictions were generated
len(y_pred)

> 150
```

### 정확도 평가

#### Logistic regression

```python
from sklearn import metrics
print(metrics.accuracy_score(y, y_pred))

> 0.96
```

#### KNN(K=5)

```python
from sklearn.neighbors import KNeighborsClassifier
knn = KNeighborsClassifier(n_neighbors=5)
knn.fit(X, y)
y_pred = knn.predict(X)
print(metrics.accuracy_score(y, y_pred))

> 0.966666666667
```

#### KNN(K=1)

```python
knn = KNeighborsClassifier(n_neighbors=5)
knn.fit(X, y)
y_pred = knn.predict(X)
print(metrics.accuracy_score(y, y_pred))

# overfitting(과적합) problem
> 1.0
```

# 4.과적합 문제를 위한 train/test 데이터 분리하기

```python
from sklearn.cross_validation import train_test_split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.4, random_state=4)
```

training 데이터로 훈련하기

```python
logreg = LogisticRegression()
logreg.fit(X_train, y_train)

> LogisticRegression(C=1.0, class_weight=None, dual=False, fit_intercept=True,
          intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
          penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
          verbose=0, warm_start=False)
```

test 데이터로 예측하기

```python
y_pred = logreg.predict(X_test)

# compare actual response values (y_test) with predicted response values (y_pred)
print(metrics.accuracy_score(y_test, y_pred))

> 0.95
```

KNN(K=5)

```python
knn = KNeighborsClassifier(n_neighbors=5)
knn.fit(X_train, y_train)
y_pred = knn.predict(X_test)
print(metrics.accuracy_score(y_test, y_pred))

> 0.966666666667
```

KNN(K=1)

```python
knn = KNeighborsClassifier(n_neighbors=1)
knn.fit(X_train, y_train)
y_pred = knn.predict(X_test)
print(metrics.accuracy_score(y_test, y_pred))

> 0.95
```

다양한 tuning parameters 로 정확도 살펴보기

```python
# try K=1 through K=25 and record testing accuracy
k_range = range(1, 26)
scores = []
for k in k_range:
    knn = KNeighborsClassifier(n_neighbors=k)
    knn.fit(X_train, y_train)
    y_pred = knn.predict(X_test)
    scores.append(metrics.accuracy_score(y_test, y_pred))
```

정확도를 시각화 하기

```python
# import Mathplotlib (scientific plotting library)
import matplotlib.pyplot as plt

# allow plots to appear within the notebook
%matplotlib inline

# plot the relationship between K and the testing accuracy
plt.plot(k_range, scores)
plt.xlabel('Value of K for KNN')
plt.ylabel('Testing Accuracy')
```

![Accuracy]({{ base }}/assets/images/hyper-accuracy.png)

# 5.파라메터 튜닝, 모델 Selection, feature Selection을 위한 Cross-validation

## Cross-validation 예: 파라메터 튜닝

```python
from sklearn.cross_validation import cross_val_score

# 10-fold cross-validation with K=5 for KNN
knn = KNeighborsClassifier(n_neighbors=5)
# cv => use 10-fold cross-validation
scores = cross_val_score(knn, X, y, cv=10, scoring='accuracy')
print(scores)

> [ 1.          0.93333333  1.          1.          0.86666667  0.93333333
  0.93333333  1.          1.          1.        ]
```

정확도의 평균

```python
print(scores.mean())

> 0.966666666667
```

KNN 에서 최적의 K 값을 찾기

```python
k_range = range(1, 31)
k_scores = []
for k in k_range:
	knn = KNeighborsClassifier(n_neightbors=k)
	scores = cross_val_score(knn, X, y, cv=10, scoring='accuracy')
	k_scores.append(scores.mean())

for i, score in enumerate(k_scores):
	print('{}:{}'.format(i, score))

> 1:0.96
2:0.9533333333333334
3:0.9666666666666666
4:0.9666666666666666
...
...
28:0.9533333333333334
29:0.9533333333333334
30:0.9533333333333334
```

정확도 시각화 하기

```python
import matplotlib.pyplot as plt
%matplotlib inline

# plot the value of K for KNN (x-axis) versus the cross-validated accuracy (y-axis)
plt.plot(k_range, k_scores)
plt.xlabel('value of K for KNN')
plt.ylabel('Cross-Validated Accuracy')
```

![Accuracy]({{ base }}/assets/images/hyper-accuracy1.png)

K=20 에서 정확도가 최대값임.

## Cross-validation: 모델 Selection

KNN

```python
# 10-fold cross-validation with the best KNN model
knn = KNeighborsClassifier(n_neighbors=20)
print(cross_val_score(knn, X, y, cv=10, scoring='accuracy').mean())

> 0.98
```

Logistic regression

```python
# 10-fold cross-validation with logistic regression
from sklearn.linear_model import LogisticRegression
logreg = LogisticRegression()
print(cross_val_score(logre, X, y, cv=10, scoring='accuracy').mean())

> 0.953333333333
```

KNN 모델이 Logistic Regression 보다 정확도가 높으므로 KNN 모델이 적합.

## Cross-validation: feature Selection

```python
import pandas as pd
import numpy as np
from sklearn.linear_model import LinearRegiression

# read in the advertising dataset
data = pd.read_csv('http://www-bcf.usc.edu/~gareth/ISL/Advertising.csv', index_col=0)

# create three feature names
feature_cols = ['TV', 'Radio', 'Newspaper']

X = data[feature_cols]

y = data.Sales

# 10-fold cross-validation with all three features
lm = LinearRegression()
scores = cross_val_score(lm, X, y, cv=10, scoring='mean_squared_error')
print(scores)

# scikit-learn issue!
> [-3.56038438 -3.29767522 -2.08943356 -2.82474283 -1.3027754  -1.74163618
 -8.17338214 -2.11409746 -3.04273109 -2.45281793]

# fix the sign of MSE scores
mse_scores = -scores
print(mse_scores)

> [3.56038438 3.29767522 2.08943356 2.82474283 1.3027754  1.74163618
 8.17338214 2.11409746 3.04273109 2.45281793]

# convert from MSE to RMSE
rmse_scores = np.sqrt(mse_scores)
print(rmse_scores)

> [ 1.88689808  1.81595022  1.44548731  1.68069713  1.14139187  1.31971064
  2.85891276  1.45399362  1.7443426   1.56614748]

# average RMSE
print(rmse_scores)

> 1.69135317081

# 10-fold cross-validation with two features
feature_cols = ['TV', 'Radio']
X = data[feature_cols]
print(np.sqrt(-cross_val_score(lm, X, y, cv=10, scoring='mean_squared_error')).mean())

> 1.67967484191
```

three features('TV', 'Radio', 'Newspaper') 보다 two features('TV', 'Radio') 가 정확도가 더 높다.
