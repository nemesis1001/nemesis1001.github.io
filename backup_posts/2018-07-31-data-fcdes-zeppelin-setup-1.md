---
layout: post
title:  "Zeppelin 설치하기"
subtitle:   "Zeppelin 설치하기"
categories: data
tags: fcdes
comments: true
---

## Zeppelin 설치하기

> 사전 설치: git <br>
 OS 정보 : mac OS <br>
 목표 설치: <br>
 - maven 3.3.9 <br>
 - zeppelin v0.8.0-rc5

> 확인하기 <br>
If you haven't installed Git and Maven yet, check the Build requirements section and follow the step by step instructions from there.

결국 zeppelin 빌드해서 사용하기 - Pyspark 등 더 잘되고 더 최신의 Spark 버전 포함


1. maven 설치(maven 설치가 안된 경우에만)
`https://zeppelin.apache.org/docs/latest/install/build.html#build-requirements`<br>
`$ wget http://www.eu.apache.org/dist/maven/maven-3/3.3.9/binaries/apache-maven-3.3.9-bin.tar.gz`<br>
`$ sudo tar -zxf apache-maven-3.3.9-bin.tar.gz -C /usr/local/`<br>
`$ sudo ln -s /usr/local/apache-maven-3.3.9/bin/mvn /usr/local/bin/mvn`

2. 제플린 git clone <br>
 `https://github.com/apache/zeppelin` 에서 clone하기

3. 제플린 설치 폴더 이동 <br>
 `$ git clone https://github.com/apache/zeppelin.git`


4. ~/zeppelin`$ git status` <br>
On branch master <br>
Your branch is up-to-date with 'origin/master'. <br>
nothing to commit, working tree clean <br>

5. ~/zeppelin`$ git checkout v0.8.0-rc5`


6. ~/zeppelin`$ git status`<br>
HEAD detached at v0.8.0-rc5
nothing to commit, working tree clean


7. ~/zeppelin`$ mvn clean package -DskipTests`

8. 웹에서 localhost:8080 확인

9. sc확인, csv파일 불러오기 확인

10. java.lang.NoSuchMethodError: 발생시 아래 링크 참조<br>
[java.lang.NoSuchMethodError 해결](https://twowinsh87.github.io/data/2018/08/01/error-zeppelin-NoSuchMethodError/)

<br>
**많은 시간이 걸립니다. 완료!<br>
그리고 현재 [공식 홈페이지](https://zeppelin.apache.org/)에서 정식 v0.8 이용이 가능합니다.**  

**Zeppelin log 보기**
`$ cd /zeppelin-0.7.3-bin-netinst/logs`
`$ tail -f 호스트명.log.날짜`
