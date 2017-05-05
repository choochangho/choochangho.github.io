---
layout: post
title:  "Spark Python 쉘을 Jupyter Notebook에서 사용하기 2"
categories: spark jupyter python
date:   2017-05-05 20:00
permalink: /archivers/jupyter-spark2
---

## 실행

```bash
hadoop-user$ PYSPARK_DRIVER_PYTHON=jupyter \
PYSPARK_DRIVER_PYTHON_OPTS="notebook --no-browser --port=7777" \
pyspark --packages com.databricks:spark-csv_2.10:1.1.0
```

*pyspark : pyspark 경로


## 브라우저에서 확인

http://localhost:777

## jupyter notebook 에서 확인

![Accuracy]({{ base }}/assets/images/jupyter-spark-example.png)