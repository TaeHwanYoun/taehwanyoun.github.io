---
layout: post
title: (venv) 2. pyenv 설치후, 가상환경 실행하기
date : 10 April 2023
category : Linux_Env
comments : true
---
### 배경 상황
앞단계에서, `pyenv`를 활용하여, 복수의 파이썬 버전을 사용할 수 있게끔 셋업을 끝냈다면,
pipenv를 활용한 가상환경을 구축하고, 원하는 특정 버전의 python 버전으로 프로젝트를 구축해보려합니다.
(pipenv 외에도,  virtualenv 등을 활용한 가상환경도 가능합니다.)


<center>  
<img src = '/assets/Linux_Env/pyenv_2/pyenv_2_pipenv_01.png' width = '80%'> <br>
pyenv & pipenv를 활용하여, 디렉토리별로 다른 환경 구축
</center>



<br>

---

# 1. pip 경로 확인
 - pyenv로 python을 설치해주었고, 문제가 없다면 pip는 .pyenv에 설치된 python의 경로를 바라봐야합니다. 
 - 그러나 간혹 기존에 system에 설치되었던 python을 여전히 바라보고 있어, pipenv 가상환경을 설치시 여전히 os에 설치된 system버전의 python으로 가상환경이 생성되는 경우가 있음. 따라서 가상환경 생성 전, 해당 디렉토리가 어떤 파이썬 인터프리터를 바라보고 있는지 확인 합니다.

### 1) python version 확인
```sh
which python
# /usr/bin/python
pyenv versions 
# * system (set by /home/th/.pyenv/version)
```

### 2) pipenv version 확인
 : pip 버전에 맞는 python 버전이 있기에, 바라보고 있는 python 버전과 pip 버전이 서로 맞아야함.
```sh
pipenv --version
# pipenv, version 2018.11.26
```

<br>

---

# 2. pipenv 가상환경 설치 & 실행
 : 가상환경은 계정 상위 폴더에서 전체로 진행하기보다는 하위 디렉토리(프로젝트)별로 생성하기를 추천.
```sh
### 1) pipenv 설치
pipenv install --python {버전명} # ex) pipenv install --python 3.6.9 

### 2) pipenv 가상환경 실행
pipenv shell
```

<br>  

---
# 3. Pipfile 패키지 관리
 - 앞서 `pipenv install`을 실행할 때, 해당 디렉토리 내에 `.Pipfile`이 존재한다면, pipenv는 `.Pipfile`을 기준으로 패키지들을 함께 설치하게 됩니다.
 - 그러나 상황에 따라, `.Pipfile`을 뒤늦게 Pull하게 될 수도 있고, 패키지들이 설치되어 있지 않을 수 도 있기에, 수동으로 `.Pipfile`내의 패키지들을 설치하라고 명령해야 할 수 도 있습니다.
  - 이때 `.Pipfile`과 관련된 파일들은 아래와 같습니다.
    * (1) **`.Pipfile.sh`** : .Pipfile.toml을 실행하라는 실행파일 (실행 방법 $ .Pipfile.sh 그대로 입력)
    * (2) **`.Pipfile.toml`** : 해당 Pipenv 가상환경이 유지할 패키지들을 작성해둔 파일로, .Pipfile을 생성하는 파일
    * (3) **`.Pipfile`** : 현재 가상환경에 설치할 패키지들을 명시한 파일 (.Pipfile.toml을 실행하면, 해당 문서에 작성된 버전들이 명시됨)

<br>  

---
# 4.pipenv 가상환경 삭제
```sh
## 1) 해당 가상환경이 설치된 디렉토리로 이동
## 2) 가상환경 삭제
pipenv --rm
```



#### Reference
[1] [01) pyenv와 가상환경](https://wikidocs.net/10936)  
[2] [pyenv란? pyenv 사용하기](https://scshim.tistory.com/602)  
[3] [[python] pyenv? virtualenv?](https://velog.io/@ryu_log/pyenv-virtualenv)