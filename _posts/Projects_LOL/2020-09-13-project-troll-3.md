---
layout: post
title:  "[프로젝트] lol gcp구축하기"
subtitle:   "[프로젝트] lol gcp구축하기"
categories: project
tags: troll
comments: true
---

> # GCP Infra 구성하기

> 퇴사 후에 프로젝트를 진행하면서 익숙한 AWS보다 GCP를 써보기로 했다.  
> 어차피 매커니즘은 같으니까..  
> 사실은 300$를 주기 때문에 대략 한달~한달반은 무료로 운영이 가능하다는 이점 때문에..


## 구성도

<img src="https://github.com/twowinsh87/twowinsh87.github.io/blob/master/assets/project_lol/%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8_GCP_1.png?raw=true" width="60%">

- private subnet zone으로 gcp 서울 리전에서 `asis-northeast3-a` 사용하기
	- 이상적이지 않은 것은 알고 있음. 
- 아래의 구성으로 구성하고, 추후에 쿠버네티스 환경으로 변경 목표
	- a,b,c 존에서 이상적으로 늘려나가기 
- Kafka, CouchBase 같은 경우 Clustering 목적

## VPC 구성

### VPC 네트워크 생성

<img src="https://github.com/twowinsh87/twowinsh87.github.io/blob/master/assets/project_lol/%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8_GCP_2.png?raw=true" width="60%">

- IPv4 CIDR 즉 사이더만 잘 정의해준다
<img src="https://github.com/twowinsh87/twowinsh87.github.io/blob/master/assets/project_lol/%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8_GCP_3.png?raw=true" width="60%">


## NAT GateWay
- 연결 : `없음` -> Cloud NAT 게이트웨이 서비스도도 하나의 인스턴스가 역할을 함. 기존 VM에 연결하지 않음(게이트웨이용 머신을 하지 않을 것이라면..)

<img src="https://github.com/twowinsh87/twowinsh87.github.io/blob/master/assets/project_lol/%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8_GCP_6.png?raw=true" width="60%">

- 하이브리드 연결 -> Cloud 라우터 -> 만들기
- `네트워크`: 만들어 놓은 네트워크로 설정
- `경로`: 커스텀 경로 만들기 

<img src="https://github.com/twowinsh87/twowinsh87.github.io/blob/master/assets/project_lol/%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8_GCP_7.png?raw=true" width="60%">


- 네트워크 서비스 - Cloud NAT - 시작하기

<img src="https://github.com/twowinsh87/twowinsh87.github.io/blob/master/assets/project_lol/%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8_GCP_8.png?raw=true" width="60%">


- 연결되었는지 확인하기
<img src="https://github.com/twowinsh87/twowinsh87.github.io/blob/master/assets/project_lol/%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8_GCP_9.png?raw=true" width="60%">


##  VM 인스턴스
- VM 인스턴스
<img src="https://github.com/twowinsh87/twowinsh87.github.io/blob/master/assets/project_lol/%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8_GCP_4.png?raw=true" width="60%">

- 네트워킹 설정(만들어 놓은 VPC로 지정)
	- 외부 IP `없음` -> 외부 IP가 필요없는 경우에 선택
	- 
<img src="https://github.com/twowinsh87/twowinsh87.github.io/blob/master/assets/project_lol/%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8_GCP_5.png?raw=true" width="60%">

프로젝트_GCP_5


## 방화벽

```
VPC를 새로 구축했기 때문에  default 방화벽 규칙을 적용할 수 없음
방화벽을 구축해서 접속하고, 수신이 가능하게 함
```

<img src="https://github.com/twowinsh87/twowinsh87.github.io/blob/master/assets/project_lol/%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8_GCP_11.png?raw=true" width="60%">


## IAM 및 관리자
- 외부 IP가 없으므로 ssh 접속이 불가능한 상황

<img src="https://github.com/twowinsh87/twowinsh87.github.io/blob/master/assets/project_lol/%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8_GCP_10.png?raw=true" width="60%">



## gcloud 설치(mac 기준)
[공식문서 참고하기](https://cloud.google.com/sdk/docs/downloads-versioned-archives?authuser=2) 

- `$ wget https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-sdk-307.0.0-darwin-x86_64.tar.gz?authuser=2`

- `$ tar -zxvf google-cloud-sdk-307.0.0-darwin-x86_64.tar.gz\?authuser=2`

- `$ cd google-cloud-sdk`

- `$ ./install.sh`

- `$ ./google-cloud-sdk/bin/gcloud init`

- google 계정을 선택 > Please enter numeric choice or text value (must exactly match list item)
	-  `enter`

- Please enter a value between 1 and 2, or a value present in the list 에 따라서 위에 기술된 프로젝트를 선택함. 질문에 따라서 시작


## 접속하기
- 방법1. vm -> ssh 연결
- 방법2. vm -> gcloud 명령 보기 -> local 터미널에 붙이기
