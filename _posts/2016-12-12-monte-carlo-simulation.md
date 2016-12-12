---
layout: post
title:  "Monte-Carlo simulation 을 이용하여 PI 구하기"
categories: anaconda python matplotlib 통계
date:   2016-12-12 09:56
permalink: /archivers/monte-carlo-pi
---

# 정의

난수를 이용하여 함수의 값을 확률적으로 계산하는 알고리즘을 부르는 용어.

# 절차


1. 직교 좌표에 반지름 $$r$$ 인 원이 위치하고 있다고 가정하자.
2. 원의 중심은 (0,0) 이라 하자.
3. 원을 감싸는 사각형을 생객해보자.
4. 3의 영역에 난수로 생성된 좌표로 점을 찍어보자.

$$
\begin{align}
\text{원안에 점이 찍힐 확률(P)} & = \frac{\text{원의 면적}}{\text{사각형의 면적}} \\
& = \frac{\pi * r * r}{2r * 2r} \\
& = \frac{\pi}{4}

\end{align}
$$

$$
\begin{align}
\pi & = 4 * P \\
& = 4 * \frac{원안에 위치하는 횟수}{만들어진 전체 점의 수}
\end{align}
$$

전체 시행횟수가 1000번이고 그 중 원안에 점이 위치하는 횟수가 300 이라면...

$$
\begin{align}
\pi & = 4 * P \\
& = 4 * \frac{300}{1000}
\end{align}
$$

점이 원안에 위치하는 경우는 점($$x$$,$$y$$)의 좌표가 원점(0, 0)으로 부터의 거리가 1보다 작거나 같은 경우이다.

즉,

$$
\sqrt{(x - 0)^2 + (y - 0)^2} = \sqrt{x^2 + y^2} \le 1
$$

이를 코드로 작성하자.


## Using NumPy


```python
import numpy as np

def ms_pi(num_of_points, num_of_try):
    total_pi = 0

    for i in range(num_of_try):
        inner_circle = 0;
        x = np.random.uniform(0, 1, num_of_points).tolist()
        y = np.random.uniform(0, 1, num_of_points).tolist()
        # 원의 내/외부 확인
        for j in range(num_of_points):
            d = np.sqrt(x[j] * x[j] + y[j] * y[j])
            # 원의 내부라면..
            if d <= 1:
                inner_circle += 1
        inner_circle = float(inner_circle)
        calculated_pi = inner_circle * 4 / num_of_points
        total_pi += calculated_pi

    return total_pi / num_of_try

ms_pi(1000, 100)

> 3.1380800000000004

ms_pi(10000, 1000)

> 3.1421176000000015

```


## Using random

```python
import random
import math
random.seed(a=2)

def generate_randomarray(num_of_elements, start_num, end_num):
    x = []
    for i in range(num_of_elements):
        x.append(random.uniform(start_num, 1))
    return x

def ms_pi2(num_of_points, num_of_try):
    total_pi = 0

    for i in range(num_of_try):
        inner_circle = 0;
        x = generate_randomarray(num_of_points, 0, 1)
        y = generate_randomarray(num_of_points, 0, 1)
        # 원의 내/외부 확인
        for j in range(num_of_points):
            d = math.sqrt(x[j] * x[j] + y[j] * y[j])
            # 원의 내부라면..
            if d <= 1:
                inner_circle += 1
        inner_circle = float(inner_circle)
        calculated_pi = inner_circle * 4 / num_of_points
        total_pi += calculated_pi

    return total_pi / num_of_try

ms_pi2(1000, 100)

> 3.1407999999999987

ms_pi2(10000, 1000)

> 3.141710400000003

```