---
layout: post
title:  "[리눅스(Linux)] redirection"
subtitle:   "[리눅스(Linux)] redirection"
categories: etc
tags: itknowledge
comments: true
---

### [리눅스(Linux)] redirection

> 리눅스 명령어에 취약한 나로써는 permission denied가 발생하면, 항상 힘들었다. 이번에 정리해서 문제를 해결하자  

#### 문제발생
- echo 1 > /tmp/file 에서 permission denied가 발생했다.
- 문제점은 알고 있으나 해결책을 위해서 정리한다.

#### 해결
- sudo -s 를 통해서 root으로 login 해서 해결한다
- shell을 sudo로 실행해서 redirection을 포함한 명령을 수행한다
- tee 명령을 sudo로 실행하여 redirection을 수행한다

> shell을 sudo로 실행  

- $ sudo sh -c "echo 1 > /tmp/file"

> tee 명령을 sudo로 실행

- echo 1 | sudo tee /tmp/file


참고: http://egloos.zum.com/studyfoss/v/5204344

<br>

---

#### tee 명령어
- 표준 입력으로부터 읽어서 표준 출력이나 파일로 쓴다
- tee [option] [file]
- option
	- -a : 덮어쓰지 않고 주어진 파일에 표준 입력을 추가한다
	- -i : 인터럽트를 무시한다
- ex) ```$ ls -l /etc | tee etclists.out```
	- /etc 디렉토리 리스트를 etclists.out 파일에 저장한다  

참고: http://msryu.tistory.com/entry/%EB%A6%AC%EB%88%85%EC%8A%A4-tee-%EB%AA%85%EB%A0%B9%EC%96%B4-%EC%82%AC%EC%9A%A9%EB%B2%95
