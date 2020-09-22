---
title: "[MLOps 기초] DVC(Data Version Control) - PART1. Data Versioning"
date: 2020-09-22
permalink: /dev/post12
tags : data-science MLOps
categories: machine-learning
---

# About DVC
들어가기 전에… 

MLOps라는 용어가 요즘 미디엄에서 자주 등장한다. DevOps의 개념을 ML/DL 분야에 특화 및 확장한 것으로, 웹/앱 개발과 같은 프로젝트에서 코드의 버전 관리가 중요했듯이, ML/DL 프로젝트에서도 버전 관리가 중요하다. MLOps가 기존의 DevOps와 다른 점은 **코드 뿐만 아니라 모델을 생성하기 위한 데이터와 모델 자체의 버전 또한 관리**해야 한다는 것이다. 이를 위해 git과 비슷하게 사용할 수 있는 좋은 툴이 있어서 사용법을 정리한다.

dvc 는 **ML project (머신러닝 프로젝트)를 위한 오픈소스 버전관리시스템**으로, ML 모델과 데이터를 tracking 하기 위한 툴이다. 

dvc 를 통해 ML 모델과 데이터를 공유 가능하고(shareable) 재현가능한(reproducible) 상태로 쉽게 만들 수 있다. 또한, 대용량 데이터 파일을 관리하고 모델 성능 측정값 또한 관리할 수 있다. 

<img src="/assets/images/dvc-icon.png" width="300" height="300">


dvc 는 크게 4가지 주요 기능을 제공한다. 

- **데이터 버전관리 (Data versioning)** 

> DVC의 코어 파트라고 할 수 있다. 대용량 파일, 데이터, ML 모델의 버전을 관리하고 공유 할 수 있다. 핵심은 ‘Git for data’, 즉 Git으로 직접 대용량 파일을 저장하지 않으면서 데이터를 관리하는 방법이다. 

- **데이터 접근 (Data access)**

> 이전 프로젝트에서 버전관리를 한 데이터 파일(데이터, 모델 등)을 또 다른 프로젝트에서 접근하는 방법이다. ML 모델의 특정 버전을 다운로드하여 서버에 반영하거나 다른 프로젝트에서 모델을 불러오는 방법을 제공한다. 

- **데이터 파이프라인 (Data pipeline)**

> 데이터나 모델이 어떻게 빌드되고 효과적으로 reproduce하는 방법이다. 데이터나 모델에 대한 “Makefiles”를 만든다고 생각하면 된다. 

- **실험 (Experiments)**

> 모델에 파라미터(parameters), 평가방법(metrics), 플롯(plots) 을 추가하는 방법에 대해 설명한다. 핵심은 “Git for ML”로, Git 에서 모델을 실험하는 방법이다. 

이제, 하나씩 알아보자. 

---

## 데이터 버전관리 (Data Versioning)

dvc 로 데이터 버전 관리하는 방법에 대해 알아본다. <br>
( 설치는 환경에 따라서 알아서 해봅시다 😁  ) <br>
나는 pyenv  가산환경에서 `pip install dvc` 로 설치했다 <br>

---
 
- **초기화** 🍽

> 버전 관리에 들어갈 디렉토리를 초기화 해준다 

```bash
cd MyProjectDir
git init # 이미 되어 있다면 생략해도 된다!
dvc init 
# .dvc 디렉토리가 생성된다
```

---

- **저장소 설정** 🏖

> remote 저장소를 만든다. 테스트를 위해 local 디렉토리를 사용했지만, <br>
> 프로젝트를 진행할 때는 다른 사람들과 공유해야 하므로 클라우드 스토리지가 좋을 듯 하다

[remote 저장소 목록 보기](https://dvc.org/doc/command-reference/remote/add#supported-storage-types)

```bash
# remote 저장소 설정
dvc remote add -d [데이터폴더] [리모트주소]
# 확인해보기
cd .dvc
cat config
"""
[core]
    remote = [데이터폴더]
['remote "data"']
    url = [리모트주소]
"""
```

---

- **버전관리** 📨

    - **add**  📬:   변경사항을 시스템에 **추가** [add 커맨드 상세보기](https://dvc.org/doc/command-reference/add)

    ```bash
    dvc add [데이터파일] # 또는 [디렉토리]를 add 할 수도 있음
    # .gitignore에 [데이터파일] 또는 [디렉토리] 추가됨
    # __.dvc 파일로 디렉토리 또는 파일이 관리됨! 
    # 즉, [데이터파일] 또는 [디렉토리] 자체가 아니라 데이터파일정보(.dvc)를 git 으로 올리게 됨!
    ```

    - **push** 📫:  remote 저장소에 **올리기**   [push 커맨드 상세보기](https://dvc.org/doc/command-reference/push)

    ```bash
    dvc push # 또는 dvc push [데이터파일].dvc 로 특정 파일을 지정할 수 있음

    """
    dvc push copied the data ~cached~ locally to the remote storage we set up earlier. 
    You can check that the data has been stored in the DVC remote with
    """

    ls -R /dvc-data-remote
    ea

    dvc-data-remote/ea:
    534c8827099ffba3a8c85fb2b59340
    ```

    -  **pull** 📥: remote 저장소 에 저장된 내용 **내려받기** [pull 커맨드 상세보기](https://dvc.org/doc/command-reference/pull)

    ```bash
    rm [데이터파일] # 또는 rm -rf [디렉토리]
    dvc pull # 또는 dvc pull [데이터파일].dvc 로 특정 파일을 지정할 수 있음
    "
    새로운 개발 환경에서 데이터를 받아오고 싶을 경우, 
    git 저장소를 clone 후 dvc pull 로 데이터를 가져오면 됨!
    "
    ```

---

다음으로 데이터 접근(Data Access) 에 대해 알아보자. <br>
[dvc 공식 홈페이지](https://dvc.org/) 에서 직접 알아봐도 된다. 🤣


