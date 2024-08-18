# 󰏢 관계형 데이터베스의 종류


## 1. key-value Database

- key-value 형태로 데이터를 저장

* 용도
    - 자주쓰는 데이터 캐싱
    - 채팅을 위한 pub/sub
    - 영상 스트리밍
    - 로그인 기록

* 예시)
    - redis

## 2. Relational Database

- 테이블 형태로 데이터를 저장
- SQL(Structured Query Language)을 사용
- 데이터를 **정규화**해서 저장함
- ACID 특성을 가짐
    - 롤백 할 수 있기 떄문에 정확도가 중요한 서비스라면 사용

* 용도
    - 사용자 정보
    - 게시판 글

* 예시)
    - mysql
    - mariadb
    - postgresql

## 3. Graph Database

- 그래프 형태로 데이터를 저장
- 노드와 노드를 연결하는 **관계**를 중심으로 데이터를 저장
- 관계를 빠르게 탐색할 수 있음

* 용도
    - 친구 추천
    - 소셜 네트워크
    - 추천 시스템

* 예시)
    - neo4j

## 4. Document Database

- 디렉토리와 json파일 형태로 저장
- 정규화 안함
- 데이터 분산이 쉬움

* 예시)
    - mongodb
    - couchdb
    - firebase

## 5. Column-family Database

- 테이블을 열로 저장
- 열마다 다른 column을 가질 수 있음
- 전용 언어인 CQL(Cassandra Query Language)을 사용, SQL과 비슷
- 보통 정규화 없이 사용
- 복제, 분산 잘함

* 용도
    - 로그 데이터
    - 시계열 데이터

* 예시)
    - cassandra
    - hbase

## 6. Search Engine

- 검색을 위한 데이터베이스
- 데이터 검색을 위한 인덱스 정보만 저장
- 실제 데이터를 저장하는 DB와 함께 사용하여 검색만 빨리 할 수 있게 활용
- 검색 결과를 빠르게 보여줄 수 있음

* 용도
    - 검색 서비스
    - 검색어 오타 교정
    - 블로그 검색

* 예시)
    - elasticsearch
    - Amazon CloudSearch
