---
layout: post
title:  "Spark Python 쉘을 Jupyter Notebook에서 사용하기"
categories: spark jupyter python
date:   2017-04-21 08:00
permalink: /archivers/jupyter-spark
---

## Spark / Python / (Hadoop) 버전


| 구분                    |   Version   |
|------------------------|-------------|
| Spark                  |   2.1.0     |
| Python                 |   3.5.2     |
| (Hadoop)               |   2.7.3     |

## 출처

[https://github.com/mozilla/jupyter-spark](https://github.com/mozilla/jupyter-spark)

## jupyter-spark 설치

**- Spark 과 Hadoop 설치 계정이 hadoop-user 라 가정하고 진행한다.**

**- Python 은 3.5.2 버전을 이용한다.**


```bash
hadoop-user$ pip3 jupyter-spark
hadoop-user$ jupyter serverextension enable --py jupyter_spark
hadoop-user$ jupyter nbextension install --py jupyter_spark --user
hadoop-user$ jupyter nbextension enable --py jupyter_spark
hadoop-user$ jupyter nbextension enable --py widgetsnbextension
```

## 설치된 extension 확인

```bash
hadoop-user$ jupyter nbextension list
Known nbextensions:
  config dir: /home/hadoop-user/.jupyter/nbconfig
    notebook section
      jupyter-js-widgets/extension  enabled
      - Validating: OK
      jupyter-spark/extension  enabled
      - Validating: OK
hadoop-user$ jupyter serverextension list
config dir: /home/hadoop-user/.jupyter
    jupyter_spark  enabled
    - Validating...
      jupyter_spark  OK
```

## 서버 통신 성능 개선을 위한 추가 패키지 설치

```bash
hadoop-user$ pip3 install lxml
```

## jupyter notebook 실행

Python3 를 spark 에서 사용하기 때문에 아래와 같이 지정하자.

```bash
hadoop-user$ export PYSPARK_PYTHON=python3
```

spark 마스터 노드(master)를 지정하여 jupyter notebook을 실행 시킨다.

```bash
hadoop-user$ jupyter notebook --Spark.url="http://master:4040"
```


> OSError: [Errno 99] Cannot assign requested address

에러가 발생하면

```bash
hadoop-user$ jupyter notebook --Spark.url="http://master:4040" --ip 0.0.0.0
```
과 같이 실행시킨다.

## 코드 작성

![Accuracy]({{ base }}/assets/images/jupyter-spark.png)

실행 중인 job 을 아래와 같이 확인 할 수 있다.

![Accuracy]({{ base }}/assets/images/code1.png)

![Accuracy]({{ base }}/assets/images/jupyter-toolbar.png)

툴바의 가장 오른쪽 버튼을 클릭하면 

![Accuracy]({{ base }}/assets/images/list-of-jobs.png)

job 목록을을 볼 수 있다.


## 전체 코드

```python
SPARK_HOME = "/home/hadoop-user/spark-2.1.0-bin-hadoop2.7"
PY4J = "python/lib/py4j-0.10.4-src.zip"
PYSPARK = "python/pyspark/shell.py"

import os
import sys

spark_home = os.environ.get('SPARK_HOME', SPARK_HOME)
sys.path.insert(0, spark_home + '/python')

sys.path.insert(0, os.path.join(spark_home, PY4J))

with open(os.path.join(spark_home, PYSPARK)) as f:
    code = compile(f.read(), "shell.py", 'exec')
    exec(code)

text_file = sc.textFile("hdfs://master:9000/user/hadoop-user/spark/ratings.csv")
text_file.count()

```

