---
layout: post
Title:  "[Ubuntu] CouchDB Install with Trouble Shooting"
---
## CouchDB란?
```cpp
아파치 카우치DB(Apache CouchDB)는 스케일러블 아키텍처를 쉽게 이용하고 보유하는데 초점을 둔 오픈 소스 데이터베이스 소프트웨어이다. 도큐먼트 지향 NoSQL 데이터베이스 구조를 갖추고 있으며 얼랭으로 구현되어 있다. JSON을 사용하여 데이터를 저장하고, 자바스크립트를 쿼리 언어로 사용(맵리듀스 사용)하며 API를 위해 HTTP를 사용한다.
```
- - -
## Check Up Before Install CouchDB
필요 패키지 설치
```cpp
$ sudo apt-get update
$ sudo apt-get install snapd jq
```
- - -

## CouchDB install
설치 및 리스트 확인
```cpp
$ sudo snap install couchdb
$ sudo snap list
```
![](https://i.imgur.com/eE4FiSE.jpg)
- - -
CouchDB 구성 파일 편집
```cpp
$ vi /var/snap/couchdb/5/etc/local.ini
```
문맥 찾기 및 수정
```cpp
[chttpd]
;bind_address = 127.0.0.1
```
to
```cpp
[chttpd]
bind_address = 0.0.0.0
```
활성화
![](https://i.imgur.com/W5bpqyO.jpg)
- - -

## CouchDB 동작 확인
서비스 실행 및 확인
```cpp
$ sudo snap start couchdb.server
$ netstat -nl | grep -E -i "proto|5984"
```
![](https://i.imgur.com/rBofKPl.jpg)

- - -
브라우저 접속 및 싱글 노드 설정
```cpp
http://192.168.56.102:5984/_utils#setup
```
![](https://i.imgur.com/5KmFDdf.jpg)

- - -
관리자 계정 및 암호 설정
![](https://i.imgur.com/OHLuN9f.jpg)
- - -
CouchDB 서비스와의 통신 테스트
```cpp
curl -s -X GET http://192.168.56.102:5984 | jq
```
![](https://i.imgur.com/C0dz6b1.jpg)

- - -

새 데이터베이스 생성 및 조회
```cpp
curl -s -X PUT "http://hwang:1234@192.168.56.102:5984/commandtest"
curl -s -X GET "http://192.168.56.102:5984/_all_dbs" | jq
```
![](https://i.imgur.com/lx9JDNA.jpg)
주소앞에 관리자계정과 비밀번호 입력을 해야 액세스 거부를 당하지 않음

- - -
예시
```cpp
curl -s -X PUT "http://127.0.0.1:5984/testdb"
```
![](https://i.imgur.com/GqMaalz.jpg)

- - -
사용할 수 있는 모든 노드 조회
```cpp
curl -s -X GET "http://hwang:1234@192.168.56.102:5984/_membership" | jq
```
![](https://i.imgur.com/aoeSkLM.jpg)

- - -
관리 권한이 있는 계정 만들기
```cpp
curl -X PUT "http://hwang:1234@192.168.56.102:5984/_node/couchdb@127.0.0.1/_config/admins/subadmin" -d '"5678"'
```
여기서 subadmin이 아이디 이고 5678이 비밀번호이다.
- - -
최종브라우저 조회
![](https://i.imgur.com/xw18ncx.jpg)
- - -
## Trouble Shooting
CouchDB 설치 및 동작확인 까지 마친후에
이를 Elasticsearch와 연동하기 위해 구글링을 하여
Elasticsearch-river-CouchDB라는 plugin을 찾았는데
계속 플러그인 설치 진행중 오류가 발생하였고
확인한 결과 해당 서비스는 이미 종료한지 오래된 것이었다.
```cpp
This plugin has been closed as of June 7, 2018 and is not available for download.
Reason: Unused.
```
해당 서비스 소스가 올라온 깃허브 사이트도 6년전에 업데이트가 끝난 상황이었다.
하여 현재는 logstash 또는 java와 붙일 방법을 모색하고 있다.
- - -



- 참조
https://github.com/elastic/elasticsearch-river-couchdb
https://wpsocket.com/plugin/couchdb/
