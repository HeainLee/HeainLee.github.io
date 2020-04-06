---
title: "[MacOS] python3 설치방법 / pyenv로 가상환경 별 프로젝트 관리"
date: 2020-03-11
permalink: /dev/post01
tags : python
categories: python
---

이 포스팅은 내 MacOS에 python3를 어떻게 설치했는지에 대한 내용을 정리한 것이다.

**그렇다. 드디어 맥북프로를 샀다!!🤣**

<figure class="half">
    <img src="/assets/images/medhat-dawoud-f-3mUXFLY2o-unsplash.jpg">
    <img src="/assets/images/jay-wennington-loAgTdeDcIU-unsplash.jpg">
</figure>

사긴 샀는데, 자판 + 배터리 하자가 있는 제품이 뽑혀서 배송받고도 며칠간은 사용도 못해봤다..ㅋㅋ 투바센터에서 하루만에 수리가 되어서 지금은 잘 쓰고 있다.


아무튼 맥북프로를 산 이유는 집에 데스크탑도 없고 서브 노트북도 없어서 간혹 재택근무를 할 때 매번 회사에서 노트북을 가져와야 하는 불편함을 없애고 싶었고, 무엇보다 MacOS를 써보고 싶었다. 


대학원 때부터 윈도우에서 개발환경을 구축해서 사용했는데, 회사에서 리눅스 서버로 작업을 하면서 윈도우와 다른 환경에 흥미로웠다. 그렇다면 그냥 노트북 아무거나 사서 리눅스를 깔면 되지 않나? 싶었지만, 리눅스는 어차피 일하면서 많이 쓰기 때문에 또 다른 운영체제인 MacOS를 택했다. 사실 맥의 운영체제가 리눅스 계열이기 때문에 단축키 등에 익숙해지면 업무에도 꽤나 유용할거 같다.


잡담은 여기까지하고 지금부터 나의 맥북프로에 python3를 설치한 방법을 정리해본다.


<br>
<br>


# 01. Homebrew 설치

맥에 python3를 설치하는 방법은 몇 가지가 있지만, 나는 Homebrew를 통해 설치했다. Homebrew는 패키지 관리 시스템으로, 리눅스에서 apt-get과 비슷하다.

Homebrew를 설치하기 전에 Xcode를 설치한다.

```bash
$ xcode-select --install
```

그러면 이제 진짜 Homebrew를 설치한다. 사실 이때 당황스러웠던게 [공식사이트][공식사이트] 에서는 아래와 같이 터미널에 복사하라고 하고, 다른 블로그에서는 `/bin/bash -c ` 대신에 `ruby -c` 또는 `/usr/bin/ruby -e`  라고 적혀 있었기 때문. 나는 아래와 같이 설치했다.
 
```bash
$ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
```

터미널에 Installation successful! 이라고 뜰 것이다. `brew --version` 를 통해 버전을 확인해본다.

```bash
$ brew --version
Homebrew 2.2.10
> Homebrew/homebrew-core (git revision 2575b; last commit 2020-03-11)
```

brew를 통해 설치된 list를 확인하는 방법은 `brew list` 이며 설치 직후에는 아무것도 뜨지 않는다.


<br>
<br>


# 02. python3 설치

brew를 통한 python3 설치는 매우 간단하다.

```bash
$ brew install python3
```

설치가 완료되면 `brew list`의 결과가 다음처럼 보일 것이다.

```bash
$ brew list
> gdbm   openssl@1.1   python   readline   sqlite   xz
```

python3 버전을 확인해보면 다음과 같다.

```bash
$ python3 --version
> Python 3.7.6
```


<br>
<br>


# 03. 파이썬 가상환경 사용하기
지금까지 파이썬을 사용하면서 가장 귀찮았던 부분은 역시나 버전관리였다. 운영체제마다 가상환경 사용법도 달랐고, virtualenv, pyenv 또는 conda를 사용하는 방법까지 많은 옵션을 저울질해서 항상 그때마다 다른 방법을 사용하기도 했다.


현재 윈도우 노트북에서는 conda와 virtualenv를 혼합해서 쓰고 있고, 회사 리눅스 서버에서는 conda를 사용하거나 자체적으로 샌드박스 개념의 서버를 쓰고 있다. (정확히는 쿠버네티스를 통해서 프로젝트 생성/관리를 한다.) virtualenv가 conda보다 편하기는 하지만 파이썬 버전별 가상환경 생성이 불가능하고, conda는 일단 무거워서 별로 mac에 깔고 싶지 않다. 다행히 맥에는 pyenv를 사용할 수 있다! 


내 맥북에는 **pyenv** 를 통해 가상환경 설정 및 프로젝트 관리를 해줄 생각이다. 참고로 포에트리(Peotry)를 사용해서 프로젝트 관리까지 해보려고 했으나, 그건 좀 더 필요하다고 생각될 때 설치해 볼 예정이다. 참고로 포에트리는 고(Go)와 러스트(Rust)에서 사용되었던 올인원 프로젝트 관리 툴이다. requirements.txt 보다 좀 더 멋지게 여러 곳에서 일괄적인 빌드가 가능하다고 한다. [포에트리 공식사이트][포에트리 공식사이트] 에서 매우 자세하게 설치, 사용법 등이 정리되어 있다.

내 맥북에서 설치하고 사용하는 방법 다음과 같다.

## 03-01. pyenv 설치

- **Homebrew 업데이트**

```bash
$ brew update
$ brew upgrade
```
- **pyenv 설치**

```bash
$ brew install pyenv # pyenv 설치 
```
- **pyenv 환경변수 설정**

```bash
# 환경변수 설정 - 방법1
$ echo ‘export PYENV_ROOT=“$HOME/.pyenv”’ >> ~/.bash_profile
$ echo ‘export PATH=“$PYENV_ROOT/bin:$PATH”’ >> ~/.bash_profile
$ echo ‘eval “$(pyenv init -)”’ >> ~/.bash_profile
# 환경변수 설정 - 방법2
$ cat .bash_profile # export PYENV_ROOT=“$HOME/.pyenv”
$ cat .bash_profile # export PATH=“$PYENV_ROOT/bin:$PATH”
$ cat .bash_profile # eval “$(pyenv init -)”
```

> 참고로 bash_profile이 없으면 생성해주면 된다. 

```bash
$ vi ~/.bash_profile
```

- **pyenv 설정파일 실행**

```bash
$ source ~/.bash_profile
```

- **pyenv 설치 점검**

```bash
$ pyenv install --list # 다운로드  가능한  파이썬  버전  목록  보기
$ pyenv install --list | wc -l # 다운로드  가능한  파이썬  버전  갯수
$ pyenv --version # pyenv 버전 확인
$ pyenv versions # 설치된 모든 python 버전 확인 
$ pyenv version # 현재 파이썬 버전
```

> pyenv 명령어 

<figure>
    <img src="/assets/images/pyenv-howto.png">
</figure>

- ### pyenv를 통한 파이썬 설치

```bash
$ pyenv install [python version] # 특정 파이썬 버전 설치
```


<br>
<br>


## 03-02. pyenv-virtualenv 설치

- ### virtualenv 설치/환경변수 설정 및 설정파일 실행

```bash
$ brew install pyenv-virtualenv
# $ echo  'eval "$(pyenv virtualenv-init -)"'  >> ~/.bash_profile
$ vi ~/.bash_profile  # 'eval "$(pyenv virtualenv-init -)"'
$ source ~/.bash_profile
```

- ### 가상환경  생성  및  실행

```bash
$ pyenv virtualenv [python version] [environment name] # 가상환경 생성
$ pyenv activate [environment name] # 가상환경 실행
$ pyenv deactivate # 가상환경 종료
```


<br>
<br>


## 03-03. 프로젝트 버전 관리

```bash
$ mkdir django_project # 프로젝트 폴더 생성
$ cd django_project # 프로젝트 폴더 이동
$ pyenv install 3.6.8 # 파이썬3.6.8 설치
$ pyenv virtualenv 3.6.8 django_env # 가상환경 생성
$ pyenv local django_env # 현재 디렉토리에 특정 가상환경 지정
```

[공식사이트]:  https://brew.sh/
[포에트리 공식사이트]: https://python-poetry.org/docs/basic-usage/
