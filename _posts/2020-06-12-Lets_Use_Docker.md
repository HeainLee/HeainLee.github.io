---
title: "도커 컨테이너 생성 및 개발환경 세팅하기"
date: 2020-06-12
permalink: /dev/post08
tags : docker rabbitmq
categories: install
---



## 01. 초기 도커 컨테이너 생성 및 이미지로 commit하기

```bash
# ubuntu 이미지 가져오기 
docker pull ubuntu:18.04

# 도커 이미지 목록 보기
docker images

# 우분투 이미지로 컨테이너 생성 
docker run -i -t --name ubuntu_1804 ubuntu:18.04

# Ctrl+P, Ctrl+Q 를 입력하면 실행상태를 유지한채로 호스트 PC 의 터미널로 빠져 나올수 있음
# 혹은 docker run 실행할 때 -d 옵션을 주고 exit 또는 Ctrl+D 로 빠져나오면 됨
docker run -i -t --name ubuntu_1804 -d ubuntu:18.04

# 컨테이너를 외부에 노출하고 싶으면 -p 옵션을 줘서 컨테이너의 포트를 호스트의 포트와 바인딩해 연결할 수 있게 설정한다
docker run -i -t --name ubuntu_1804 -p 80:80 ubuntu:18.04

# -v옵션은 호스트와 컨테이너의 디렉토리를 연결 (마운트) 할 때 사용한다 
docker run -i -t --name ubuntu_1804 -v /home/myfolder:/home/myfolder ubuntu:18.04

# 컨테이너 목록 확인
docker ps 

# 사용중인 컨테이너 이미지로 올리기
# docker commit -m "메세지" [컨테이너 이름 or 컨테이너 아이디] [이미지이름]:[태그]
docker commit -m "my image" ubuntu_1804 my_ubuntu:latest
```

## 02. 우분투 도커 생성 후 초기 세팅

```bash
# 초기 기본 세팅 (패키지 설치는 필요한 거 설치)
apt-get update
apt-get install vim
apt-get install sudo
apt-get install openjdk-8-jdk

# pyenv 설치 (파이썬3 설치해도 되지만 관리가 편한 pyenv가 좋다)

## 1. 필요한 패키지 설치
### Common build problems를 참고하면 Build 관련해서 문제가 생길 때 설치해야 할 패키지
apt-get install -y build-essential libssl-dev zlib1g-dev libbz2-dev \
libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev \
xz-utils tk-dev libffi-dev liblzma-dev python-openssl git

## 2. pyenv 설치 및 환경변수 설정 
git clone https://github.com/pyenv/pyenv.git ~/.pyenv
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.bashrc
# exec "$SHELL"
vi ~/.bashrc 
# source ~/.bashrc

## 3.pyenv-virtualenv 설치 및 환경변수 설정
git clone https://github.com/yyuu/pyenv-virtualenv.git ~/.pyenv/plugins/pyenv-virtualenv
echo 'eval "$(pyenv virtualenv-init -)"' >> ~/.bashrc

## 4. pyenv 사용하기
pyenv versions
pyenv install 3.6.8
pyenv virtualenv 3.6.8 django_env
pyenv local django_env


export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"

# postgreql 설치
sudo apt install postgresql postgresql-contrib
sudo -i -u postgres
psql

## 외부접속..
postgres=# \conninfo
vi /etc/postgresql/10/main/postgresql.conf 
>> 다음을 추가 listen_addresses = '*'
>> 재시작 sudo service postgresql restart
vi /etc/postgresql/9.3/main/pg_hba.conf
>> IPv4 local connection의 ADDRESS를 0.0.0.0:0 로 수정

## 패스워드 변경
alter user postgres with password 'postgres'

## 새로운 데이터베이스/유저 생성 
createuser dasolution --interactive
postgres=# CREATE DATABASE analytic_module OWNER dasolution ENCODING 'utf-8' ;
postgres=# ALTER ROLE dasolution WITH PASSWORD 'dasolution' ;
### 연결확인
psql -U dasolution -d analytic_module -W -h localhost
# -U : username
# -d : database name
# -W : password 사용
# -h: 연결할 호스트. 제외할 경우 "peer authentication failed" 에러 발생 가능
# See : https://www.lesstif.com/dbms/postgresql-61899197.html

# 장고 개발환경 설정
pip install -r requirements.txt
# psycopg2==2.8.5 install 안됨 —> pip install psycopg2-binary?
python manage.py runserver 0.0.0.0:8022 # 포트 열려있어야 함
celery -A my_project worker -l info

# rabbitmq 설치

## 1. Install prerequisites
sudo apt-get install curl gnupg -y

## 2. Install RabbitMQ signing key
curl -fsSL https://github.com/rabbitmq/signing-keys/releases/download/2.0/rabbitmq-release-signing-key.asc | sudo apt-key add -

## 3. Install apt HTTPS transport
sudo apt-get install apt-transport-https

## 4. Add Bintray repositories that provision latest RabbitMQ and Erlang 21.x releases
sudo tee /etc/apt/sources.list.d/bintray.rabbitmq.list <<EOF
## Installs the latest Erlang 22.x release.
## Change component to "erlang-21.x" to install the latest 21.x version.
## "bionic" as distribution name should work for any later Ubuntu or Debian release.
## See the release to distribution mapping table in RabbitMQ doc guides to learn more.
deb https://dl.bintray.com/rabbitmq-erlang/debian bionic erlang
deb https://dl.bintray.com/rabbitmq/debian bionic main
EOF

## 5. Update package indices
sudo apt-get update

## 6. Install rabbitmq-server and its dependencies
sudo apt-get install rabbitmq-server -y --fix-missing

service rabbitmq-server start
>  * Starting message broker rabbitmq-server  

# Create User in RabbitMQ
sudo rabbitmqctl add_user dasol dasol
# Set Tags
# sudo rabbitmqctl set_user_tags dasol administrator
# Grant necessary permissions
sudo rabbitmqctl set_permissions -p / dasol “.*” “.*” “.*”
# Verify permissions
sudo rabbitmqctl list_permissions
# others
rabbitmqctl list_users
rabbitmqctl status

```

- **기타 도커관련 명령어**

```bash
# 도커 로그 확인
docker logs --tail 10 ${CONTAINER_ID}

# 컨테이너 이름 변경하기 
docker rename [기존 이름] [변경 하고자 하는 이름]

# 컨테이너 삭제
docker rm [컨테이너 이름]

# 이미지 삭제
docker rmi [이미지이름]:[이미지태그]
```
- **기타 리눅스 명령어**

```bash
# 사용자별 명령어 로그
vi /home/{사용자계정명}/.bash_history
# root 계정의 명렁어 로그
vi /root/.bash_history
# 버전 보기
cat /etc/issue
# 사용자 목록 보기
cat /etc/passwd
# 포트 보기
netstat -nlp | grep 2020

# 사용자 추가 
sudo adduser [사용자이름]
su [사용자이름] 
vi /etc/sudoers

# root 유저 비번 설정
sudo passwd root

# 만약 unicode 에러가나면 export LC_ALL=C.UTF-8 실행 / locale -a 로 확인
export LC_ALL=C.UTF-8
locale -a 

# 현재 디렉토리 포함&하위 목록 보기 
find . -type d # 폴더 목록
find . -type f # 파일 목록 
```

- **rabbitmq관련 명령어**

```bash
# rabbitmq 시작/중지/상태보기
service rabbitmq-server start
service rabbitmq-server stop
service rabbitmq-server status

# Management UI 플로그인 활성화
# RabbitMQ는 Management 라는 UI 관리 도구를 제공한다. 이 기능을 사용하기 위해서는 RabbitMQ Plugin을 활성화 시켜야 한다.
rabbitmq-plugins enable rabbitmq_management

```

> 포트설정

RabbitMQ는 클라이언트와 CLI Tool 연결을 위해 몇가지 포트를 사용한다.
따라서 방화벽을 사용하고 있다면 아래 포트를 열어줘야 한다.

- 4369 : epmd, 여러 rabbitmq 서버끼리 서로를 찾을 수 있는 네임 서버 역할을 하는 데몬에서 사용
- 5672, 5671 : AMQP 를 사용한 메시지 전달
- 25672 : inter-node 와 CLI Tool 연결
- 15672 : HTTP API, Management UI

> 가상호스트

RabbitMQ 는 connection, exchange, queue, binding, user, policy 들을 virtual hosts 를 통해 논리적인 그룹으로 분리해서 운영할 수 있다.

RabbitMQ 는 vhost 단위로 자원에 대한 권한을 갖는다. 따라서 사용자는 전체 권한을 갖을 수 없고 하나 이상의 vhost 단위로 권한을 부여 받는다.

vhost 마다 이름이 지정되고, 클라이언트는 지정한 vhost 에 대한 연결을 맺는다. 이 연결을 통해 오직 해당 vhost의 exchange, queue, binding 만 접근할 수 있게 된다.

```bash
rabbitmqctl add_vhost my_host #vhost 생성 
rabbitmqctl set_permissions -p my_host my_name ".*" ".*" ".*" # 특정 사용자에게 특정 vhost 접속 권한 설정 
```
- **참고한 사이트 목록 :**
[Ubuntu에서 pyenv, virtualenv 사용하기](https://twpower.github.io/38-install-pyenv-and-virtualenv-on-ubuntu)
[Ubuntu에서 RabbitMq 설치1](https://www.rabbitmq.com/install-debian.html)
[Ubuntu에서 RabbitMq 설치2](https://jonnung.dev/rabbitmq/2019/01/30/rabbitmq-installation-on-ubuntu/)