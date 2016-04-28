---
layout: post
title:  "Mac OS 에서 python virtualenv 사용하기"
categories: mac python virtualenv
date:   2016-04-28 09:30
permalink: /archivers/mac-python-virtualenv
---

### Installation Virtualenv using pip

python 이 설치되어 있다는 가정하에 수행 한다.

```shell
$ pip install virtualenv
$ pip install virtualenvwrapper
```

### 가상환경 저장 경로 만들기

앞으로 생성하게될 가상환경은 모두 아래의 경로에 저장된다.

```shell
$ mkdir ~/.virtualenvs
```

.bashrc 파일을 열어 아래와 같이 추가한다.

```shell
export WORKON_HOME=~/.virtualenvs
source /usr/local/bin/virtualenvwrapper.sh
```

변경 사항 반영하기

```shell
source .bashrc
```

### 가상환경 만들기

작업할 폴더로 이동 후 테스트용 가상환경을 만들어 보자.

```shell
# python3 대신 파이썬 실행파일 경로를 지정해도 된다.
# test_env 이름의 폴더가 ~/.virtualenvs 에 저장된다.
$ mkvirtualenv --python=python3 test_env
# or
$ mkvirtualenv --python=/usr/local/bin/python3 test_env
# 가상 환경 생성과 동시에 가상 환경이 활성화 된다.
(test_env) $

### 가상환경 활성 / 비활성

```shell
# 활성화
$ workon test_env
(test_env) $
# 비활성화 
(test_env)$ deactivate
```

### 가상환경 hook 파일

```shell
$ cd ~/.virtualenvs/
$ ls -alh
total 96
drwxr-xr-x  16 choochangho  staff   544B  4 28 09:59 .
drwxr-xr-x+ 78 choochangho  staff   2.6K  4 28 09:59 ..
-rwxr-xr-x   1 choochangho  staff   135B  4 27 10:36 get_env_details
-rw-r--r--   1 choochangho  staff    96B  4 27 10:36 initialize
-rw-r--r--   1 choochangho  staff    73B  4 27 10:36 postactivate
-rw-r--r--   1 choochangho  staff    75B  4 27 10:36 postdeactivate
-rwxr-xr-x   1 choochangho  staff    66B  4 27 10:36 postmkproject
-rw-r--r--   1 choochangho  staff    73B  4 27 10:36 postmkvirtualenv
-rwxr-xr-x   1 choochangho  staff   110B  4 27 10:36 postrmvirtualenv
-rwxr-xr-x   1 choochangho  staff    99B  4 27 10:36 preactivate
-rw-r--r--   1 choochangho  staff    76B  4 27 10:36 predeactivate
-rwxr-xr-x   1 choochangho  staff    91B  4 27 10:36 premkproject
-rwxr-xr-x   1 choochangho  staff   130B  4 27 10:36 premkvirtualenv
-rwxr-xr-x   1 choochangho  staff   111B  4 27 10:36 prermvirtualenv
drwxr-xr-x   8 choochangho  staff   272B  4 27 13:14 test_env
```

가상환경의 hook 파일

```shell
$ workon test_env
(test_env) $ cd $VIRTUAL_ENV/bin
$ ls -alh
total 216
drwxr-xr-x  24 choochangho  staff   816B  4 27 13:25 .
drwxr-xr-x   8 choochangho  staff   272B  4 27 13:14 ..
drwxr-xr-x   3 choochangho  staff   102B  4 27 13:13 __pycache__
-rw-r--r--   1 choochangho  staff   2.0K  4 27 13:12 activate
-rw-r--r--   1 choochangho  staff   1.0K  4 27 13:12 activate.csh
-rw-r--r--   1 choochangho  staff   2.2K  4 27 13:12 activate.fish
-rw-r--r--   1 choochangho  staff   1.1K  4 27 13:12 activate_this.py
-rwxr-xr-x   1 choochangho  staff   300B  4 27 13:13 django-admin
-rwxr-xr-x   1 choochangho  staff   159B  4 27 13:13 django-admin.py
-rwxr-xr-x   1 choochangho  staff   267B  4 27 13:12 easy_install
-rwxr-xr-x   1 choochangho  staff   267B  4 27 13:12 easy_install-3.5
-rwxr-xr-x   1 choochangho  staff   150B  4 27 13:12 get_env_details
-rwxr-xr-x   1 choochangho  staff   239B  4 27 13:12 pip
-rwxr-xr-x   1 choochangho  staff   239B  4 27 13:12 pip3
-rwxr-xr-x   1 choochangho  staff   239B  4 27 13:12 pip3.5
-rw-r--r--   1 choochangho  staff   137B  4 27 13:24 postactivate
-rw-r--r--   1 choochangho  staff    74B  4 27 13:12 postdeactivate
-rwxr-xr-x   1 choochangho  staff    99B  4 27 13:25 preactivate
-rw-r--r--   1 choochangho  staff    75B  4 27 13:12 predeactivate
lrwxr-xr-x   1 choochangho  staff     7B  4 27 13:12 python -> python3
-rwxr-xr-x   1 choochangho  staff   2.3K  4 27 13:12 python-config
-rwxrwxr-x   1 choochangho  staff    25K  4 27 13:12 python3
lrwxr-xr-x   1 choochangho  staff     7B  4 27 13:12 python3.5 -> python3
-rwxr-xr-x   1 choochangho  staff   246B  4 27 13:12 wheel

(test_env) $ cat postactivate 
#!/bin/bash
# This hook is sourced after this virtualenv is activated.

export DJANGO_SETTINGS_MODULE="myproject.settings.development"

(test_env) $ 

```