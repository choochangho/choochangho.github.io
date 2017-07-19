---
layout: post
title:  "HDInsight SSH 터널링을 이용하여 Ambari 웹서비스 접근하기"
categories: azure HDInsight Ambari
date:   2017-07-19 10:10
permalink: /archivers/azure-hdinsight-ssh-tunnel
---

> https://docs.microsoft.com/ko-kr/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel

# SSH 터널링 없이 Ambari 웹서비스 접근

HDInsight 는 배포 이후 웹서비스(https) 로 접근 가능한  서비스는 Ambari 가 있다.

다만, Ambari 가 Hadoop 에서 기본적으로 제공하는 WEB UI를 포함한 아래의 목록에 포함되는 서비스 접근이 안된다.

- JobHistory
- NameNode
- Thread Stacks
- Oozie web UI
- HBase Master and Logs UI

이는, 외부에 직접 공개되는 위험을 막기 위함이다.

이를 접근 하기 위해서는 SSH 터널링을 이용하여야 한다.

# SSH 터널링 방법

- SSH 명령어 이용
- Putty를 사용하여 터널 만들기

여기서는 SSH 명령어를 이용하도록 하겠다.

SSH 명령어는 bash on windows10 의 ssh 를 이용하도록 하겠다.
커맨드 창을 열고 bash 에 진입 후 아래와 같은 명령어를 입력하자.

```
ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
```

![ssh-tunnel]({{ base }}/assets/images/2017-07-19/00.png)


이제 브라우저의 요청을 Proxy로 사용하도록 아래와 같이 설정을 변경 한다(파이어폭스).

![proxy-setup]({{ base }}/assets/images/2017-07-19/01.png)

# 브라우저 주소 입력

http://headnodehost:8080

![proxy-setup]({{ base }}/assets/images/2017-07-19/02.png)

![proxy-setup]({{ base }}/assets/images/2017-07-19/03.png)