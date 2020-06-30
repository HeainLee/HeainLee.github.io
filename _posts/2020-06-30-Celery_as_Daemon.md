---
title: "샐러리 데몬으로 실행하는 방법"
date: 2020-06-30
permalink: /dev/post10
tags : python ubuntu celery
categories: code
---


## 샐러리 데몬으로 실행하는 방법

우분투 환경에서 샐러리를 init-scipt 사용해서 데몬으로  실행시키는 방법을 정리한 내용입니다.

공식문서가 다소 불친절해서...🤔 결국 구글링으로 참고하기에 좋은 블로그를 찾았고🧐 그 내용을 정리한 것이다.


- [참고한 블로그(친절)](https://pythad.github.io/articles/2016-12/how-to-run-celery-as-a-daemon-in-production)  /  [공식문서(불친절)](https://docs.celeryproject.org/en/latest/userguide/daemonizing.html#daemonizing) / [추가로 보면 좋을 블로그1](https://kitcharoenp.github.io/celery/2019/03/11/celery_worker_as_daemon.html)

### 00. 서버에 등록된 서비스 확인 방법

```bash 
service —status-all
```

<br>
<br>


### 01. Init-script: celeryd

- **celeryd 등록**

1. `/etc/init.d` 경로에 `celeryd` 파일 생성
2. [celery 깃헙](https://github.com/celery/celery/blob/master/extra/generic-init.d/celeryd) 에 나온 celeryd 스크립트 복붙
3. 다음 명령어를 터미널에 입력
```bash
sudo chmod 755 /etc/init.d/celeryd
sudo chown root:root /etc/init.d/celeryd
```

- **celeryd 설정파일 등록**

1. `/etc/default`  경로에 `celeryd`  파일 생성
2. [celeryd 설정 옵션](https://docs.celeryproject.org/en/latest/userguide/daemonizing.html#available-options)에서 각 프로젝트에 맞게 설정 옵션 등록


<br>
<br>


### 02. Init-script: celerybeat

- **celerybeat 등록**

1.  `/etc/init.d`  경로에  `celerybeat`  파일 생성
2. [celery 깃헙](https://github.com/celery/celery/blob/master/extra/generic-init.d/celerybeat)에 나온 celerybeat 스크립트 복붙
3. 다음 명령어를 터미널에 등록
```bash
sudo chmod 755 /etc/init.d/celerybeat
sudo chown root:root /etc/init.d/celerybeat
```

- **celerybeat 설정파일 등록**

celeryd 설정 했던 것과 같이  `/etc/default` 경로에서 `celerybeat` 파일을 생성해서 등록하거나, 기존의 `celeryd` 하위에 추가해서 사용해도 됨


<br>
<br>


### 03. 등록된 서비스 사용방법
```bash 
sudo /etc/init.d/celeryd start {status|stop|restart}
# 또는
sudo service celeryd start {status|stop|restart}
```

<br>
<br>
<br>
<br>

**⚙︎ 설정파일 옵션 예시 ⚙︎**

```bash
# Absolute or relative path to the 'celery' command:
CELERY_BIN="/home/dasolution/.pyenv/versions/3.6.8/envs/django_env/bin/python3.6 -m celery"

# App instance to use
CELERY_APP="dasolution"

# Names of nodes to start
CELERYD_NODES="worker"
# Also start multiple and configure settings
#CELERYD_NODES="worker1 worker2 worker3"

# Where to chdir at start.
CELERYD_CHDIR="/home/dasolution/"

# Extra command-line arguments to the worker
CELERYD_OPTS="-c 4"
# Configure node-specific settings by appending node name to arguments:
#CELERYD_OPTS="--time-limit=300 -c 8 -c:worker2 4 -c:worker3 2 -Ofair:worker1"

# Set logging level to INFO
CELERYD_LOG_LEVEL="INFO"

# %n will be replaced with the first part of the nodename.
# CELERYD_LOG_FILE="/var/log/celery/%n%I.log"
# CELERYD_PID_FILE="/var/run/celery/%n.pid"
CELERYD_LOG_FILE="/home/dasolution/bin/celery/celery-worker-dasolution.log"
CELERYD_PID_FILE="/home/dasolution/bin/celery/celery-worker-dasolution.pid"

# Workers should run as an unprivileged user.
#   You need to create this user manually (or you can choose
#   a user/group combination that already exists (e.g., nobody).
CELERYD_USER="username"
CELERYD_GROUP="username"

# If enabled pid and log directories will be created if missing,
# and owned by the userid/group configured.
CELERY_CREATE_DIRS=1

# Where to chdir at start.
CELERYBEAT_CHDIR="/home/dasolution/"

# Extra arguments to celerybeat
# CELERYBEAT_OPTS="--schedule=/var/run/celery/celerybeat-schedule"
CELERYBEAT_LOG_FILE="/home/dasolution/bin/celery/celery-beat-dasolution.log"
CELERYBEAT_PID_FILE="/home/dasolution/bin/celery/celery-beat-dasolution.pid"
```
