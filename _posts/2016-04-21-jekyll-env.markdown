---
layout: post
title:  "Jekyll 환경 설정 분리하기"
categories: jekyll
permalink: /archivers/jekyllenv
---

Jekyll 의 환경설정 파일을 배포되는 환경에 따라 다르게 적용하고 싶을때 사용하는 방법에 대해서 알아 보자.

Jekyll 의 테마를 다운로드 받아 사용한다면 보통은 _config.yml 파일만 설정파일로 존재할 것이다.
이 파일은 production 환경에서 사용되므로 로컬 환경에 적용하기가 쉽지 않다.

이 때는 아래와 같이 _config_dev.yml 파일을 만들고 _config.yml 파일에서 override 시킬 값들을 재정의 하면 된다.

## 개발용 설정파일 정의 ##

```yaml
# _config_dev.yml
# Site settings
url: "http://localhost:4000" 
```

## Jekyll 실행 ##

로컬 환경에서 jekyll을 실행할 경우 추가된 설정파일이 로드 되도록 설정파일을 지정해야 한다.

```shell
$ jekyll serve --config _config.yml,_config_dev.yml
```