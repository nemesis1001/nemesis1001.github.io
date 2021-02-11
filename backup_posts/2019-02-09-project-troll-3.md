---
layout: post
title:  "[프로젝트] 구성 및 셋팅"
subtitle:   "[프로젝트] 구성 및 셋팅"
description : "[프로젝트] 구성 및 셋팅"
keywords : "python 버전, python 3.6.5, pyenv, 아나콘다, anaconda, 아나콘다 버전, 아나콘다 업데이트, python 삭제"
categories: project
tags: troll
comments: true
---

> ## 프로젝트  
> - 해당 게임의 버전업에 따라서 api 레벨이 바뀌었음...    
> 	- (1)api를 분석하고 맞춰서 다시 코드를 짜야하는 문제   
> - 코드 저장소를 선택했고, 협업툴, 서비스 범위까지 선택했음.  
> 	- (2)개발환경을 맞추기 위해서 도커, 언어 버전을 맞추는게 문제(파이썬 최악ㅗ)  
> - 아래는 파이썬 버전을 맞추는 과정을 정리함.  
> - 다행인점은 프로젝트 구성원 7명이 모두 맥 유저이다..ㅎㅎㅎ  


## 기존의 파이썬 버전을 삭제

```
rm -rf ~/anaconda3 ~/.conda ~/.anaconda ~/.condarc
rm ~/Library/Application Support/binstar/*anaconda*
rm ~/Library/Receipts/io.continuum.pkg.anaconda*
```

## pyenv로 관리
- 핵심은 conda 5.2 설치 = python 3.6.x
- 최근 버전 아나콘다는, python 3.7을 포함하고 있고.. 그런데 텐서플로우 지원하기 쉽지 않음.

`(1) brew install pyenv`  

`(2) vi ~/.zshrc`

- 저는 zsh을 사용중이고, bash_profile 사용자는 vi ~/.bash_profile 에..
- 아래 붙이고 source 하기

```
export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"
```

- pyenv 사용방법 (따라하지 않아도 됩니다)

```
// 설치할 수 있는 파이썬 버전들
pyenv install --list

// 특정 버전 설치
pyenv install "버전명(ex. anaconda3-5.2.0)"

// 특정 버전 삭제
pyenv uninstall "버전명"

// 설치된 파이썬 버전들
pyenv versions
```

- `(3) pyenv install anaconda3-5.2.0` // 5.2.0 버전 설치

- `(4) pyenv global anaconda3-5.2.0` // 5.2.0 전역으로 설정

- `(5) 터미널 종료 후 재시작`

- `(6)python -V //Python 3.6.5 나오면 됩니다. 5.2.0 기준`


참고: https://m.blog.naver.com/sarang2594/221310545963
