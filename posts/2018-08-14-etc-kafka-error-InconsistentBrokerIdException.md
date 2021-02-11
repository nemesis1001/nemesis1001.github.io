---
layout: post
title:  "[Kafka]kafka.common.InconsistentBrokerIdException error"
subtitle:   "[Kafka]kafka.common.InconsistentBrokerIdException error"
description : "kafka 에러 error"
keywords : "kafka, kafka.common.InconsistentBrokerIdException, InconsistentBrokerIdException"
categories: etc
tags: kafka
comments: true
---

> ## [Kafka]kafka.common.InconsistentBrokerIdException error    
> ERROR Fatal error during KafkaServer startup. Prepare to shutdown (kafka.server.KafkaServer)
kafka.common.InconsistentBrokerIdException: Configured broker.id 1 doesn't match stored broker.id 2 in meta.propert
ies. If you moved your data, make sure your configured broker.id matches. If you intend to create a new broker, you
 should remove all data in your data directories (log.dirs)  
 이슈가 발생. 원인은 broker.id=1 인스턴스로 root작업하다가 꼬여서 broker.id=2를 스냅샷을 통해서 broker.id=1로 다시 셋팅하는 과정에서 발생했다.

<br>

### 이슈
- 먼저 기본적인 broker.id=1로 셋팅 과정을 다했다.
- 현재 인스턴스에 주키퍼와 카프카를 동시에 설치해서 테스트 중.
- 그런데 주키퍼는 문제가 없었고, 카프카에서 이슈가 터졌다. shutdown

### 해결하기
- ```$ cat kafkatmp/config/server.properties | grep log.dir``` 확인
- default는 (root) /tmp/kafka-logs/ 임
- 나는 바꾼 적이 있어서 다른 폴더였음
- 각자 설정한 해당 디렉토리로 이동
- ```$ rm /각자설정한 log.dir 디렉토리/meta.properties```

위 과정을 거치면 에러는 발생하지 않는다.
