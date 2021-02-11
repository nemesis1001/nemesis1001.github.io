---
layout: post
title:  "GCP(google cloud flatform)에서 인스턴스 외부ip 고정하기"
subtitle:   "GCP(google cloud flatform)에서 인스턴스 외부ip 고정하기"
categories: etc
tags: gcp
comments: true
---

## GCP(google cloud flatform)에서 인스턴스 외부ip 고정하기

> 인스턴스 중지 후 다시 시작하면 외부아이피가 변동된다. 이부분을 해결해보자


- 인스턴스가 이미 생성되어 있는 경우
	- GCP 콘솔 - 네트워킹 - VPC 네트워크 - 외부 IP 주소
	- 아래 그림 참고
	<img src="https://github.com/twowinsh87/twowinsh87.github.io/blob/master/assets/etc_img/gcp_ip_2.png?raw=true">  

	- 고정 주소 예약 - 이름 작성 - 지역(인스턴스 설정 지역) - 연결 대상(연결하고자 하는 인스턴트 클릭) - 예약(완료)
	- 콘솔 - 인스턴스로 가서 확인해보자


- 인스턴스를 아직 생성하지 않고 초기에 셋팅하고자 하는 경우
	- GCP 콘솔 - 네트워킹 - VPC 네트워크 - 외부 IP 주소
	- 고정 주소 예약 - 이름 작성 - 지역(인스턴스 설정 지역) - 예약
	- 콘솔 - 인스턴스(생성) - 생성 중 옵션 - 네트워킹 - 네트워크 인터페이스 - 외부 IP탭(만들어 놓은 고정 IP 네임으로 설정) - 완료
