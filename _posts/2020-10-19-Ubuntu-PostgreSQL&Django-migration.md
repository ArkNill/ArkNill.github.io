---
layout: post
Title:  "[Ubuntu] PostgreSQL & Django 연동"
---
## PostgreSQL란?
```cpp
포스트그레스큐엘(PostgreSQL)은 오픈 소스 객체-관계형 데이터베이스 시스템(ORDBMS)으로, 엔터프라이즈급 데이터베이스 관리 시스템(DBMS)의 기능과 차세대 데이터베이스 관리 시스템에서나 볼 수 있을 법한 기능들을 제공한다. 약 20여 년의 오랜 역사를 갖는 포스트그레스큐엘은 다른 관계형 데이터베이스 시스템과 달리 연산자, 복합 자료형, 집계 함수, 자료형 변환자, 확장 기능 등 다양한 데이터베이스 객체를 사용자가 임의로 만들 수 있는 기능을 제공함으로써 마치 새로운 하나의 프로그래밍 언어처럼 무한한 기능을 손쉽게 구현할 수 있다.
```
- - -
## Install PostgreSQL
apt repository 등록 및 설치

```cpp
1. sudo apt-get install software-properties-common gpg

2. sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys B97B0AFCAA1A47F044F244A07FCC7D46ACCC4CF8

3. sudo add-apt-repository "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -c -s`-pgdg main"

4. sudo apt-get update

5. sudo apt-get install postgresql
```
- - -
## 서버 작동 및 확인
```cpp
service postgresql restart
service postgresql status
```
- - -
## PostgreSQL DB 생성 및 유저 생성
```cpp
sudo -i -u postgres
psql
create database django_test;
\l
\connect  django_test
create user root with password '1234';
grant all privileges on database django_test to root;
```
- - -
## Django 연동 설정
사전 바이너리 설치
```cpp
pip install psycopg2-binary
```
django 프로젝트로 들어가서 settings.py 파일의
installed_apps 항목을 찾아서 blog 추가

- - -
settings.py 파일의 database 항목 수정

- - -
app의 models.py 파일

- - -
PostgreSQL 릴레이션 확인
```cpp
\dt
```

- - -
##admin page 설정
```cpp
python manage.py createsuperuser
```
username: admin
password: 1234
로 설정

- - -
post 모델을 admin 페이지에서 보기 위해서는 이를 admin 페이지에 등록

- - -
app의 폴더로 들어가보면 admin.py 라는 파일

- - -
127.0.0.1:8000/admin

- - -
PostgreSQL에서 데이터 입력 확인
```cpp
select * from blog_post;
```

- - -

### PostgreSQL 외부 포트 접근 가능하게 하기
https://racoonlotty.tistory.com/entry/PostgreSQL-%EC%99%B8%EB%B6%80-%EC%A0%91%EA%B7%BC-%ED%97%88%EC%9A%A9?category=868396

- - -

**참조**
- https://www.postgresql.org/download/linux/ubuntu/
- https://devlog.jwgo.kr/2018/05/25/getting-start-postgres/
