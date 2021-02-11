---
layout: post
title:  "[More Kafka] Exactly Once"
subtitle:   "[More Kafka] Exactly Once"
description : "[More Kafka] Exactly Once"
keywords : "consumer, kafka consumer, consumer 옵션, kafka consumer 설정, 컨슈머, 카프카 컨슈머, znode, 카프카 신뢰성, acks, 오프셋 커밋, 복제팩터, replicationFactor,
Exactly-once, 카프카 한번처리, Exactly-once, 카프카 정확히 한번, kafka Exactlyonce"
categories: etc
tags: kafka
comments: true
---


# Exactly-once

- 다음과 같은 문제 상황에서 발생 시나리오를 생각해보자
![](https://cdn-images-1.medium.com/max/1600/1*LoYCrVC2O5tTtJ9olLkPfw.png)

1. 애플리케이션이 카프카 로그에 메시지를 기록하지만 네트워크를 통해서 확인 응답을 받지 못하면 문제가 발생함. (실제로 쓰기에는 성공했을 수도 있고, 아니면 Kafka에 도착하지 못한 경우가 있을 수 있다). 즉 프로듀서는 Kafka에서 정상응답을 받지 못하면 다시 한번 시도할 수 있으므로 중복 전송의 가능성이 생긴다
2. Consumer 입장에서 메시지를 읽고 오프셋을 업데이트 할 때, 실패하는 경우 중복처리의 문제가 발생할 수 있다

### 간단한 해결책
- 고유한 키를 지원하는 외부 시스템에 식별 데이터를 쓰는 것. RDBMS나 엘라스틱서치 등의 시스템을 사용할 수 있다.
- 레코드 자체에 고유한 키를 포함하거나 토픽,파티션,오프셋을 조합하여 고유한 키를 생성하고, 이 식별 데이터를 외부 시스템에 쓴다
- 프로듀서는 레코드를 쓰거나 컨슈머에서 읽을 때 확인하여 레코드 중복을 방지할 수 있다. = 멱등성(idempotent) 라고 한다


### Kafka summit 2018 참고
[https://www.youtube.com/results?search_query=kafka+exactly+once](https://www.youtube.com/results?search_query=kafka+exactly+once)

![](https://github.com/twowinsh87/twowinsh87.github.io/blob/master/assets/kafka_img/kafka2019-exactly-once-1.png?raw=true)

![](https://github.com/twowinsh87/twowinsh87.github.io/blob/master/assets/kafka_img/kafka2019-exactly-once-2.png?raw=true)

- 프로듀서에서 중복 저장의 문제를 해결하기 위해서
- 아래 옵션을 통해서 `pid` = producerId 와 `seq`  = sequene number를 통해서 로그안에 저장을 함. 중복이 방지된다. 필요 설정은 아래와 같다
- consumer는 commit이 정상적으로 이루어진 메세지만 가져옴
- Commit 이 없거나 Abort 된 메세지는 가져오지 않음

## kafka Transactions

![](https://github.com/twowinsh87/twowinsh87.github.io/blob/master/assets/kafka_img/kafka2019-exactly-once-3.png?raw=true)

- `producer.beginTxn();` : 트랜잭션 시작 로그 저장
- `producer.send();` : 메세지 append
- `producer.commitTxn()` :
	- 2 phase commit
	- (first) 로그 저장 시작, 브로커로 flush와 복제가 됨을 보장
	- commit 마커를 토픽 파티션에 저장
		- commit 마커가 있다는 것은 정상적으로 consume 이 가능한 것
	- (second) commit 마커가 저장된 이후에 commit transaction log = finished
- (참고) `producer.abortTxn();` : 실패시 Abort 마커를 저장해서 Consume 되지 못하도록 구분함

### Producer Config
- No Code change necessary
- `enable.idempotence`: true //default false
- `ack` : all
- `retries`:  1 보다 크게

### Consumer Config
- `isolation.level`: read_committed  // default read_uncommitted
