---
layout: post
title:  "[Docker] DB, 웹서버 셋팅으로 알아보는 포트포워딩, 컨테이너, 볼륨"
subtitle:   "[Docker] DB, 웹서버 셋팅으로 알아보는 포트포워딩, 컨테이너, 볼륨"
description : "[Docker] DB, 웹서버 셋팅으로 알아보는 포트포워딩, 컨테이너, 볼륨"
keywords : "docker, docker 명령어, msa, docker run, docker start, docker restart, 도커, docker mariadb, docker mysql, docker db, docker 포트포워딩, 컨테이너, docker 컨테이너, 도커 컨테이너, 도커 포트포워딩, 도커 ip, 도커 웹서버, 도커 볼륨, docker volume"
categories: etc
tags: docker
comments: true
---

## 도커 포트 포워딩
- 호스트 PC에서 docker port로 접근이 가능한 상태
- 혹은 외부에서 docker port로 접근할 수 있도록 해줘야함.

## 웹서버 띄우고 외부접속으로 알아보는 도커 포트포워딩

```
(1)
$ docker run -i -t --name myApacheWebServer -p [나의 Ip]:7777:80 ubuntu:14.04

(2)
/# apt-get update
/# apt-get install apache2 -y
/# service apache2 start
```

- (1)
  - ubuntu:14.04 버전을 설치, 실행한다. 내가 지정한 해당 컨테이너의 name은 myApacheWebServer 이다.
  - -i -t로 실행하면서 bash 셸을 사용할 수 있다.
  - 외부에서 나의IP:7777로 접근하면 내가 방금 설치한 ubuntu:14.04에서 띄운 아파치 웹서버로 접속이 된다.  

- (2)
  - docker names가 myApacheWebServer 인 ubuntu:14.04 에서 아파치 웹서버를 설치하고 띄운다.

- Tip(1)
  - 어떻게 shell에서 컨테이너 종료없이 나가나요?
    - 컨트롤 p, 컨트롤 q를 차례로 눌러줍니다.
    - $ docker ps 로 확인해보죠!

- Tip(2)
  - 위에서 run이 되고 만약 ubuntu:14.04 컨테이너가 죽은 경우(stop) = exit를 쓴다던가 하면,
  - start명령어로 다시 도커를 띄우고 attch로 접근 할 수 없음.
  - 다시 ubuntu:14.04의 bash셀을 가능하게 하려면

`docker exec -it [컨테이너ID 4자리 이상] bash` or `$ docker exec -i -t [이미지이름] /bin/bash` 를 통해서 bash쉘 사용이 가능하다.

## 도커 볼륨
- 컨테이너 데이터를 영속적 데이터로 활용하는 방법 = 볼륨
- 이러한 볼륨에는 여러방법이 있는데, 저는 주로 목적에 따라 호스트 볼륨 공유방법을 사용합니다.

## MySql(MariaDB)도커 셋팅으로 알아보는 호스트와 공유하는 볼륨셋팅 방법

```
docker run -d \
--name mymariadbtest \
-p 3306:3306 \
-e MYSQL_ROOT_PASSWORD=1234 \
-e MYSQL_USER=user \
-e MYSQL_PASSWORD=password \
-e MYSQL_DATABASE=mymariadb \
-v ~/Host의 디렉토리 경로/data:/var/lib/mysql \
-v ~/Host의 디렉토리 경로/conf:/etc/mysql/conf.d \
mariadb:10.3.11
```

- mariadb 10.3.11버전이 없으면 pull(다운받아서) run(실행)
- 컨테이너 이름은 mymariadbtest
- -d 옵션으로 데몬으로 실행
- 포트는 3306->3306 (호스트 3306으로 들어오면 도커포트 3306으로 매칭)
- 루트,유저,패스워드,디비정보 를 -e 옵션주고 셋팅할 수 있음
- -v 옵션으로 호스트의 볼륨(디스크저장소)과 매치(공유)를 시킬 수 있다
	- -v 옵션으로 호스트 디렉토리와 컨테이너 디렉토리와 연결
- 저 같은 경우에는 DB데이터와 환경설정 저장을 각각 다르게 매치했습니다.
	- var/lib/mysql 은 mysql 컨테이너에서 데이터를 저장하는 경로
	- etx/mysql/conf.d 는 컨테이너에서 기본 conf 경로
- 여러개의 -v 옵션으로 디렉터리단위나 파일단위로 연결할 수 있다.


## 컨테이너화?
- 각 애플리케이션을 하나의 컨테이너로 만드는 것 = 컨테이너화
  - db, 웹서버, elk 등등의 이미지를 하나의 컨테이너 안에 담는다? 정도로 생각

- 아래 그림참고.
  - 우측처럼 각 애플리케이션들을 분리하는 것이 여러모로 운영 측면에서 가장 효율적임!
  - 분리하면 초기 셋팅이 꽤나 귀찮기도 하겠습니다만..도커의 철학인 분리를 지켜보는 쪽으로...

<img src="https://github.com/twowinsh87/twowinsh87.github.io/blob/master/assets/docker_img/docker-2-1.png?raw=true" weight="400" height="200">
