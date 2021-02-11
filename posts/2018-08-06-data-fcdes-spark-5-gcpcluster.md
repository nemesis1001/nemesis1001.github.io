---
layout: post
title:  "[Spark_5]Google Cloud Platform에서 spark 클러스터 구성하기"
subtitle:   "[Spark_5]Google Cloud Platform에서 spark 클러스터 구성하기"
categories: data
tags: fcdes
comments: true
---

## [Spark]Google Cloud Platform에서 spark 클러스터 구성하기

> GCP 무료 크레딧($300)으로 클러스터를 구성해보자  
> 클러스터를 구성하는 방법은 아래 두가지가 있다(VM/클라우드)


1. VM - 리눅스 설치 - Spark
 - Stand alone
 - VM 여러대 구성, master - slave( N개)

2. 클라우드 활용(google cloud platform 활용해서 구성 실습)
 - GCP(google cloud platform) : 신규가입자 12개월 무료($300)의 장점으로 실습에 활용


#### 클라우드를 활용한 클러스터 구축 선택
1. 리눅스가 설치된 머신 - spark 셋업 - 카피(N 대) - 여러대 머신을 띄우고 - spark 클러스터를 띄우기

2. Dataproc(AWS EMR과 비슷)

3. 클러스터 구축 실습
 - 분산 모드의 경우에 Master, Slave 들이 다른 서버에 있을 경우에 각 서버들의 Secure Shell(SSH) 로그인을 수행하기 때문에 계속 password를 입력하는 등 사용자가 번거로운 상황이 발생한다. 그렇기 때문에 공개키 인증방식을 사용하자.

 - **'한대에서 여러대를 띄우는 방법 vs 여러대를 실제로 띄우는 방법'**
 - 저는 여러대(1 Master, N Worker)를 실제로 띄워서 연결하는 방식을 하겠습니다.
 - Worker를 띄우는 방식은 두 가지가 있습니다.
		- slave pc에 접속해서 띄우는 방법 -> ./sbin/start-slave.sh
		- master pc에서 slave를 띄우는 방법 -> ./sbin/start-slave.sh
		- **지금은 master pc에서 slave에 접속해서 띄우는 방법 선택**

<br>

#### GCP에서 클러스터 구축
1. 인스턴스 생성하기
 - Compute Engine -> VM인스턴스 -> 인스턴스 만들기 -> (특이사항)ubuntu 18.04LTS
2. 방화벽설정(테스트용으로 전부다 허용 목적, 상황에 맞게 커스터마이징 하기)
 - VPC 네트워크 -> 방화벽 규칙 -> 방화벽 규칙 만들기 ->
(대상)네트워크의 모든 인스턴스 -> (소스 IP 범위)0.0.0.0/0 ->(프로토톨 및 포트) 모두허용
3. spark-master(master 대상 ssh 눌러서 접속)
 - ssh-keygen을 모른다면 맨 아래 내용을 참고해주세요.
 - ```$ ssh-keygen // ssh-keygen을 생성 -> enter -> enter -> enter //cat(복사) 공개키 >> 옮길장소```
 - ```$ cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys```
 - ```$ chmod og-wx ~/.ssh/authorized_keys //ssh localhost //문제 없으면 last login 메시지와 함께 deny 메세지가 안뜸.```
4. spark 설치하기
 - ```$ wget [spark다운로드 주소]```
 - ```$ tar xvf [다운받은 spark 버전 네임.tgz]```
5. Java8 설치하기
 - ```$ sudo add-apt-repository ppa:webupd8team/java```
 - ```$ sudo apt-get update```
 - ```$ sudo apt-get install oracle-java8-installer```
 - spark-shell 구동확인하기(local) 잘 구동되면 shell skrkrl
 - ```$ ./saprk~~/bin/spark-shell```
 - 머신 종료
 - ```$ exit```
 - 머신 중지하기
6. 인스턴스 카피(workder 사용 대수 만큼)
 - (1)
 - Compute Engine ->  스냅샷
 - 스냅샷만들기 -> 이름설정 -> 소스디스크(위에 만든 머신으로 클릭) -> 만들기
 
 - (2)
 - Compute Engine -> 인스턴스 만들기 (지역, 영역설정 왠만하면 동일하게~ (목적이 없다면))
 - 부팅디스크 - 변경 - 스냅샷탭 - 만든 스냅샷으로 설정 - 만들기
 - 원하는 worker 수많큼 (6) - (2) 반복
 - master와 동일한 컨디션의 worker만들기 끝

7. master ~ slave 관계 설정하기
 - ~spark/conf 로 이동
 - ls로 slaves.template 파일 확인
 - ```$ cp -p slaves.template slaves```  
 // cp 명령어의 -p옵션은 모든 정보들이 그대로 보존되어 복사되는 옵션입니다.   
 // .template은 실행되지 않는 스트립크 이기 때문에 아래와같이 작성해주어야 합니다.
 - $ vi slaves
 - localhost 아래 줄에 클라우드로 생성한 worker의 외부 ip / 내부 ip 를 넣습니다.  
 // 내, 외부 ip주소는 구글 클라우드 - Compute Engine - VM인스턴스에 있습니다. 아래와 같은 형태로 넣습니다.  
 //# localhost <- master이면서 worker역할을 하길 원하면 #(주석)을 지우세요  
 // 333.333.111.555  
 // 333.333.333.111

8. master, slaves  띄우기
 - ```$ ./sbin/start-all.sh // 사용종료시 stop-all.sh```
 - ```$ ./bin/spark-shell // spark shell 실행```
 - ```$ jps```


#### Spark cluster UI
 - 외부IP:8080 // Spark master monitoring
 - 외부IP:808n // Worker monitoring, n =1, 2, 3 worker의 수 UI 페이지
 - 외부IP:4040 // Spark Application monitoring, 작업시간 등을 볼 수 있음


#### 예제
 - spark-shell 에서  
 - ```def df = spark.sparkContext.makeRDD(1to500000000)```
 - ```val bigDF = (0 to 10).map(_ => df).reduce(_ union _)```  
 - ```bigDF.count```


#### Worker 수에 따라서 작업시간 비교하기
 - ```$ ./conf/ 에서 worker ip를 각각 주석처리하면서(dead) count 시간을 비교해봅니다.```

<br>
---
> **이슈 발생**  

 GCP(google cloud platform)에서 스냅샷으로 인스턴스 생성하여 여러대를 구성할 때, authorized_keys 를 엎어버리는 것? 같습니다. 그래서 spark cluster가 실행되지 않는 문제가 있습니다. (permission denied) 상태.
GCP에서 해결책 찾기는 못하였고 GCP를 사용한다면 다음 포스팅 예정인 GCP의 DataProc 서비스를 사용하거나, aws의 ec2로 구성하면 문제가 없으니 aws를 사용하는 것이 좋겠습니다.  
 그리고 잘 작동하더라도 인스턴스 실습 후 중지하고, 추후 다시 재 실행 할 때마다 외부 접속 ip가 달라지므로 master conf에서 ip 등록을 다시하거나, 내부 ip로 접속하는 방법을 사용해야 할 것같습니다.  

- 해결책을 정리했습니다.[ip고정 바로가기](https://twowinsh87.github.io/etc/2018/08/07/etc-gcp-networkip/)

<br>

> 잡담  

램 사용량 설정X, default도 괜찮습니다. 성능 튜닝을 위해서 reduce 작업등을 위해서 disk에 써야하는 경우에는 설정하는 것이 좋겠습니다.  
 추가로, 실제 작업은 메모리의 70 ~ 80% 정도 사용, 영속화에 사용하는 메모리가 나머지이거나 일부분...그리하여 데이터 처리에 기본 메모리의 총 양을 사용하는 것은 아님  

<br>

> ssh-keygen [강의보기](https://opentutorials.org/course/2598/14535)  

접속하고자 하는 pc에  .pub을 옮기면 된다  
id_rsa <- 절대 노출되면 안되는 private 키  
id_rsa.pub <- 노출되어도 괜찮은 public 키  
SSH로 자동 로그인 하고 싶은 시스템의 파일 뒷부분에 ~/.ssh/authorized_keys 를 추가하면 다음 부터 Password를 입력하지 않아도 자동 로그인된다.  

---
