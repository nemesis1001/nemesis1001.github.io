---
layout: post
title:  "[리눅스(Linux)]그룹 생성, 권한 부여하기"
subtitle:   "리눅스에서 그룹 생성, 권한 부여하기"
categories: etc
tags: itknowledge
comments: true
---

## [리눅스(Linux)]그룹 생성, 권한 부여하기

> 프로젝트를 하면서 카프카, 주키퍼 서버를 구축하고  
> 구성원에게 권한을 주기위해서 정리하는 글이다.

### 리눅스 그룹 권한 주기(root)

- 그룹 만들기(groupadd [그룹명])

	- ```$ groupadd fc```

- 그룹 확인하기(groups)

	- groups
	- /etx/group 에서 그룹을 확인할 수 있다.

- 그룹 삭제하기(groupdel [그룹명])

	- ```$ groupdel fc```

<br>

### 그룹에 구성원 추가하기
- /etc/group 파일을 편집하여 직접 등록

 OR

- gpasswd [옵션] [사용자] [그룹명]

	- -a 유저명 : 새로운 멤버를 추가.

	- -d 유저명 : 멤버를 제거.

	- -r [그룹명] : 특정 그룹의 패스워드를 제거함(그룹의 정보는 /etc/gshadow 있음)

	- -R [그룹명] : 그룹에 접근을 제한함

	- -A 유저명, 유저명 [그룹명] : 그룹의 그룹관리자를 설정함

	- -M 유저명, 유저명 [그룹명]: 기존 그룹 무시하고 새로운 그룹 멤버를 설정함.

<br>

### 폴더 권한 설정하기

- 파일의 퍼미션을 변경
	- chmod [옵션] [퍼미션] [파일]	 
	- chmod -R 775 /home/test1/
	- 위의 예시는 -R 옵션은 /home/test1/ 이하 디렉토리 권한을 모두 변경
	- 775는 다 알죠?[참고하기](https://webisfree.com/2015-01-02/%EB%A6%AC%EB%88%85%EC%8A%A4-%EC%9C%A0%EB%8B%89%EC%8A%A4-%ED%8C%8C%EC%9D%BC-%EA%B6%8C%ED%95%9C-%EC%84%A4%EC%A0%95-%EC%95%8C%EC%95%84%EB%B3%B4%EA%B8%B0-777-755)

  <br>

- 파일의 소유자나 소유그룹을 변경
	- chown [옵션] [소유자:소유그룹] [파일]
	- chown -R tester:fc /home/test1/
	- 위의 예시는 -R 옵션은 /home/test1/ 이하 디렉토리 권한을
	- 소유자(tester) 소유그룹(fc)으로 모두 변경
