---
layout: post
Title:  "[Ubuntu] Elasticsearch 설치(미완)"
---
## Elasticsearch란?
```cpp
Elasticsearch는 Apache Lucene( 아파치 루씬 ) 기반의 Java 오픈소스 분산 검색 엔진입니다.

Elasticsearch를 통해 루씬 라이브러리를 단독으로 사용할 수 있게 되었으며, 방대한 양의 데이터를 신속하게, 거의 실시간( NRT, Near Real Time )으로 저장, 검색, 분석할 수 있습니다.
```
- - -
## Check Up Before Install Elasticsearch
```cpp
- Oracle VM Virtual Box 버전 6.1.14 이용
- Ubuntu 20.04.1 LTS 환경에서 설치 진행
- openjdk 8 자바 설치 필요
```
- - -

## java install
```cpp
$ sudo apt-get install openjdk-8-jre
$ sudo apt-get install openjdk-8-jdk
```
- - -

## java version confirm
```cpp
$ javac -version
==javac 1.8.0_265==
```
- - -
## Installation Progress
```cpp
공식 매뉴얼에 따라 Elasticsearch 7.9.2를 설치한다.

$ wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.9.2-amd64.deb

$ wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.9.2-amd64.deb.sha512

$ shasum -a 512 -c elasticsearch-7.9.2-amd64.deb.sha512 

$ sudo dpkg -i elasticsearch-7.9.2-amd64.deb
```
- - -

++이미지 차후 추가++

현재는 수동으로 서버를 on/off 한다.
차후의 진행도에 따라 자동부팅 설정을 결정한다.
```cpp
$ sudo service elasticsearch start
```
![](C:\IMG\)
```cpp
$ sudo service elasticsearch status
```
![](C:\IMG\)
![(C:\IMG\연결및 전체주소 확인.JPG)](C:\IMG\연결및 전체주소 확인.JPG)
![C:\IMG\연결및 전체주소 확인.JPG](C:\IMG\연결및 전체주소 확인.JPG)
```cpp
$ sudo service elasticsearch stop
```
![](C:\IMG\)
```cpp
$ curl -XGET "http://localhost:9200"
```
![](C:\IMG\)
- - -
## Elasticsearch 동작확인

ifconfig로 주소 확인
inet ==192.168.56.102==  netmask 255.255.255.0  broadcast 192.168.56.255
![](C:\IMG\)

Host Connection Confirm
![](C:\IMG\통신확인.jpg)

curl -X GET "http://192.168.56.102:9200/"
![](C:\IMG\엘라스틱서치 서버주소 확인.jpg)
curl -X POST -H 'Content-Type: application/json'  'http://192.168.56.102:9200/tutorial/techarticle/1' -d '{ "message": "ElasticSearch installation on Ubuntu Linux!" }'
![](C:\IMG\)
curl -X GET 'http://192.168.56.102:9200/tutorial/techarticle/1'
![](C:\IMG\)
curl -X PUT -H 'Content-Type: application/json' 'http://192.168.56.102:9200/tutorial/techarticle/1' -d '{ "message": "Tech Tutorials" }'
![](C:\IMG\)

- - -

### 차후 내용추가
- java 환경변수 설정 설명 추가
- .bash_aliases 파일 생성 및 환경변수 설정 설명 추가
- bash 개념설명 포스팅

- - -



- 참조
https://www.elastic.co/guide/en/elasticsearch/reference/current/deb.html#deb-key(공식 사이트 설치 가이드)

