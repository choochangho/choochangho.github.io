---
layout: post
title:  "Python3 Spark 에서 PYTHONHASHSEED 오류 발생 해결하기"
categories: spark python python3
date:   2017-05-05 20:30
permalink: /archivers/python3-spark2-PYTHONHASHSEED
---

## 관련 오류

```bash
Exception: Randomness of hash of string should be disabled via PYTHONHASHSEED

    at org.apache.spark.api.python.PythonRunner$$anon$1.read(PythonRDD.scala:166)
    at org.apache.spark.api.python.PythonRunner$$anon$1.<init>(PythonRDD.scala:207)
    at org.apache.spark.api.python.PythonRunner.compute(PythonRDD.scala:125)
    at org.apache.spark.api.python.PythonRDD.compute(PythonRDD.scala:70)
    at org.apache.spark.rdd.RDD.computeOrReadCheckpoint(RDD.scala:306)
    at org.apache.spark.rdd.RDD.iterator(RDD.scala:270)
    at org.apache.spark.api.python.PairwiseRDD.compute(PythonRDD.scala:342)
    at org.apache.spark.rdd.RDD.computeOrReadCheckpoint(RDD.scala:306)
    at org.apache.spark.rdd.RDD.iterator(RDD.scala:270)
    at org.apache.spark.scheduler.ShuffleMapTask.runTask(ShuffleMapTask.scala:73)
    at org.apache.spark.scheduler.ShuffleMapTask.runTask(ShuffleMapTask.scala:41)
    at org.apache.spark.scheduler.Task.run(Task.scala:89)
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:214)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
    ... 1 more
```

## 원인

Python 3.2.3 이상에서 str, byte 그리고 datetime 객체는 서비스 거부공격(denial-of-service)을 막기 위해
random 값의 salt 를 사용한다. 

해시값은 단일 인터프리터에서는 그 값이 일관적이지만, 세션간에 일관된 값을 제공하기 위해 시드를 설정해야 한다.

## 조치 방법

spark-env.sh 에서 PYTHONHASHSEED 값을 설정한다.

```bash
export PYTHONHASHSEED=123
```

## 테스트하기

```hash
export PYTHONHASHSEED=323
hadoop-user$ for i in `seq 1 3`;
   do
     python3 -c "print(hash('foo'))";
   done
8902216175227028661
8902216175227028661
8902216175227028661
```

위와 같이 일관된 값이 출력되어야 한다.

(*)출처 : <http://stackoverflow.com/questions/36798833/what-does-exception-randomness-of-hash-of-string-should-be-disabled-via-pythonh>

