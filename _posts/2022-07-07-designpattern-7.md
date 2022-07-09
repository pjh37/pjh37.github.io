---
layout: post
title:  " CQRS pattern"
date:   2022-07-07
desc: "command query Responsibility segregation"
keywords: "DesignPattern"
categories: [Designpattern]
tags: [designpattern]
icon: icon-html
---

# CQRS 패턴

## CQRS 란?
명령을 처리하는 책임과 조회를 처리하는 책임의 분리하는 것을 의미한다.
명령이란 시스템의 상태를 변경하는 작업을 의미하고 조회란 시스템의 상태를 반환하는 작업이다.

<br/>

## 사용 이유
기존 아키텍처에서는 데이터베이스를 명령,조회에 있어 동일한 데이터 모델을 사용한다.
간단한 CRUD에서는 상관 없지만 복잡해질 경우 읽기에서 많은 종류의 DTO를 활용한 반환
쓰기의 경우 복잡한 유효성 검사등이 포함될 수 있다.

예를 들어 한 테이블에 예약정보에 대한 다양한 값들이 있다고 할때
예약에 대해서 일주일중 가장 많은 사람들이 예약한 날짜,시간등을 조회하거나
단순히 한 유저가 예약한것에 대한 히스토리데이터 조회등 쿼리가 다양해질 수 있으며 혹은
컬럼이 계속해서 늘어날 수도 있다. 시간이 흐름에 따라 다양한 요구사항을 녹이기 위해
모델이 점점 변질될 가능성이 있다.

<br/>

![img](https://user-images.githubusercontent.com/37110261/177764011-322e9382-193e-47b0-8d5a-11796e25e7a8.png)


복잡해진 조회와 명령 쿼리로 인해 변질된 모델을 지속적으로 이용하다 보면 추후 요구사항이 늘어날때마다
모델이 재가공될 위험이 있습니다. 이를 방지 하기 위해 create,update,delete의 명령과 read의 쿼리로나누어
관리를 하는 것이 CQRS 패턴입니다.

장점
+ 독립적인 크기 조정 읽기,쓰기 디비를 독립적으로 스케일 아웃할 수 있다.
+ 최적화된 데이터 스키마, 읽기에 경우 쿼리에 최적화된 스키마를 사용, 쓰기에는 업데이트에 최적화된 스키마를 사용할 수 있다.
+ 관심사 분리를 하여 읽기,쓰기를 구분하면서 유지 가능하고 유연한 모델을 생성할 수 있다.
+ 단순한 쿼리 읽기 데이터베이스에서 구체화된 뷰를 저장하여 쿼리할때 복잡한 조인을 방지할 수 있다.

<br/>

고려 사항
+ 복잡성: 간단한 어플리케이션에 경우 CQRS구현할 필요는 없다.
+ 명령처리후 업데이트 이벤트 전송시 메시징을 사용하므로 메시지 사용에서 오는 중복,오류등을 처리해야함
+ 최종 일관성 확보가 필요하며 변경 내용의 반영에 실패,지연,중복등의 이유로 읽기 데이터가 깨질 수 있다.

![img (1)](https://user-images.githubusercontent.com/37110261/177764701-b09779d4-2644-45ec-b81b-6661484c2015.png)


## 구현

단순히 읽기와 쓰기 데이터베이스를 나누는 것 뿐만 아니라 쓰기용 디비에 일어난 변경을 어떻게
읽기 디비로 싱크를 맞춰주는지가 CQRS패턴 구현에서 주요 쟁점입니다.

## 사용기술
<img width="856" alt="debezium-architecture" src="https://user-images.githubusercontent.com/37110261/178098199-9766a80d-65f2-42dc-9cb8-756a5a233863.png">

+ CDC (Change Data Capture)
  + 테이블에 적용되는 삽입, 업데이트 및 삭제 작업을 기록
  + 테이블에 적용되는 CUD(create,update,delete)및 스키마 변경까지 bin-log에 기록되고 이를 읽어서 <br>
  kafka로 전송하고 사용하고자 하는 곳에서 consume하여 사용한다.
  + 설치에 필요한 것
    + zookpeer
    + kafka
    + mysql connector
    + mysql

<br/>

## kafka connect
Kafka connect는 카프카용 데이터 통합 프레임워크이며 이 때 Kafka connect는 Kafka connector가 동작하도록 실행해주는 프로세스이다. Kafka connector에는 Source connector와 Sink connector가 있다. 간단히 말하면 Source Connector는 Producer의 역할, Sink connector는 Consumer 역할을 한다.

Source Connector : 외부시스템 -> 커넥트 -> 카프카 <br>
Sink Connector : 카프카 -> 커넥트 -> 외부 시스템 <br>

Debezium은 카프카 커넥트(Kafka Connect) 기반의 플러그인이며 데이터 변경 캡쳐를 위해 사용된다. Debezium Connector for MySQL 를 통해 Source connector를 생성하여 mysql 데이터 변경을 topic에 저장한다.

```dockerfile
version: '2'
services:
  zookeeper:
    image: wurstmeister/zookeeper
    container_name: zookeeper
    ports:
      - "2181:2181"
      - "2888:2888"
  kafka:
    image: wurstmeister/kafka
    depends_on:
      - zookeeper
    container_name: kafka
    ports:
      - "9092:9092"
      - "29092:29092"
    environment:
      KAFKA_LISTENERS: PLAINTEXT://:9092,PLAINTEXT_HOST://:29092
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:9092,PLAINTEXT_HOST://localhost:29092
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: PLAINTEXT:PLAINTEXT,PLAINTEXT_HOST:PLAINTEXT
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_INTER_BROKER_LISTENER_NAME: PLAINTEXT
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    links:
      - zookeeper

  mysql:
    image: mysql
    container_name: mysql
    cap_add:
      - SYS_NICE
    ports:
      - "3306:3306"
    environment:
      MYSQL_HOST: mysql
      MYSQL_ROOT_PASSWORD: admin
      MYSQL_USER: mysqluser
      MYSQL_PASSWORD: mysqlpw
    volumes:
      - D:/mysql/data:/var/lib/mysql

  connector:
    image: debezium/connect
    depends_on:
      - kafka
      - mysql
    ports:
      - "8083:8083"
    environment:
      BOOTSTRAP_SERVERS: kafka:9092
      GROUP_ID: 4
      CONFIG_STORAGE_TOPIC: my_connect_configs
      OFFSET_STORAGE_TOPIC: my_connect_offsets
      STATUS_STORAGE_TOPIC: my_connect_statuses
    links:
      - zookeeper
      - kafka
      - mysql
```

<br/>

## mysql 사용자 추가및 권한 확인

```dockerfile
# 해당 명령어를 통해 docker mysql 컨테이너 내부 접속
docker exec -it mysql /bin/bash
```

``` mysql
use mysql;

// mysqluser 가 추가 되어 있는지 확인
select host, user from user;

// mysqluser 없으면 생성
CREATE USER 'mysqluser'@'%' IDENTIFIED BY 'mysqlpw';
// mysqluser 에게 권한 부여
GRANT ALL PRIVILEGES ON *.* TO 'mysqluser'@'%';
```

## kafka connect 확인

```dockerfile
# 클러스터 생성 여부 확인
curl http://localhost:8083/
```

실행된 mysql connector에 mysql과 kafka에 연결할 정보를 등록합니다.

```json
curl -i -X POST -H "Accept:application/json" -H "Content-Type:application/json" localhost:8083/connectors/ -d '{
  "name": "source-test-connector",
  "config": {
    "connector.class": "io.debezium.connector.mysql.MySqlConnector",
    "tasks.max": "1",
    "database.hostname": "mysql",
    "database.port": "3306",
    "database.user": "mysqluser",
    "database.password": "mysqlpw",
    "database.server.id": "1",
    "database.server.name": "dbserver1",
    "database.allowPublicKeyRetrieval": "true",
    "database.include.list": "testdb",
    "database.history.kafka.bootstrap.servers": "kafka:9092",
    "database.history.kafka.topic": "dbhistory.testdb",
    "key.converter": "org.apache.kafka.connect.json.JsonConverter",
    "key.converter.schemas.enable": "true",
    "value.converter": "org.apache.kafka.connect.json.JsonConverter",
    "value.converter.schemas.enable": "true"
  }
}'

```

<br/>

## 요구사항
+ 사용자는 room을 from~to 시간 동안 예약할 수 있다.
+ 사용자는 자신의 예약 history를 볼 수 있다.(취소,등록,변경 등)
+ 어느 room이 인기가 있었는지를 알기 위해 room,from,to등의 정보들을 쿼리한다.

## 실행
+ 예약 생성 요청
+ http://localhost:8080/reservations
```json
{
    "userId":"123",
    "roomId":"999",
    "from":"2020-11-10T12:34:30",
    "to":"2020-12-10T12:37:30"
}
```

+ reservation table 변경 사항 발생시 kafka listener에서 받는 데이터
+ c,u,d등으로 update,create,delete를 구분하며 payload의 before,after의 값을 이용해 변경 전,후를 파악할 수 있다.

```json
{
  "schema": {
    "type": "struct",
    "fields": [
      {
        "type": "struct",
        "fields": [
          {
            "type": "string",
            "optional": false,
            "field": "reservation_id"
          },
          {
            "type": "string",
            "optional": true,
            "field": "account_id"
          },
          {
            "type": "string",
            "optional": true,
            "field": "room_id"
          },
          {
            "type": "int64",
            "optional": true,
            "name": "io.debezium.time.MicroTimestamp",
            "version": 1,
            "field": "start_time"
          },
          {
            "type": "int64",
            "optional": true,
            "name": "io.debezium.time.MicroTimestamp",
            "version": 1,
            "field": "end_time"
          }
        ],
        "optional": true,
        "name": "dbserver1.testdb.reservation.Value",
        "field": "before"
      },
      {
        "type": "struct",
        "fields": [
          {
            "type": "string",
            "optional": false,
            "field": "reservation_id"
          },
          {
            "type": "string",
            "optional": true,
            "field": "account_id"
          },
          {
            "type": "string",
            "optional": true,
            "field": "room_id"
          },
          {
            "type": "int64",
            "optional": true,
            "name": "io.debezium.time.MicroTimestamp",
            "version": 1,
            "field": "start_time"
          },
          {
            "type": "int64",
            "optional": true,
            "name": "io.debezium.time.MicroTimestamp",
            "version": 1,
            "field": "end_time"
          }
        ],
        "optional": true,
        "name": "dbserver1.testdb.reservation.Value",
        "field": "after"
      },
      {
        "type": "struct",
        "fields": [
          {
            "type": "string",
            "optional": false,
            "field": "version"
          },
          {
            "type": "string",
            "optional": false,
            "field": "connector"
          },
          {
            "type": "string",
            "optional": false,
            "field": "name"
          },
          {
            "type": "int64",
            "optional": false,
            "field": "ts_ms"
          },
          {
            "type": "string",
            "optional": true,
            "name": "io.debezium.data.Enum",
            "version": 1,
            "parameters": {
              "allowed": "true,last,false,incremental"
            },
            "default": "false",
            "field": "snapshot"
          },
          {
            "type": "string",
            "optional": false,
            "field": "db"
          },
          {
            "type": "string",
            "optional": true,
            "field": "sequence"
          },
          {
            "type": "string",
            "optional": true,
            "field": "table"
          },
          {
            "type": "int64",
            "optional": false,
            "field": "server_id"
          },
          {
            "type": "string",
            "optional": true,
            "field": "gtid"
          },
          {
            "type": "string",
            "optional": false,
            "field": "file"
          },
          {
            "type": "int64",
            "optional": false,
            "field": "pos"
          },
          {
            "type": "int32",
            "optional": false,
            "field": "row"
          },
          {
            "type": "int64",
            "optional": true,
            "field": "thread"
          },
          {
            "type": "string",
            "optional": true,
            "field": "query"
          }
        ],
        "optional": false,
        "name": "io.debezium.connector.mysql.Source",
        "field": "source"
      },
      {
        "type": "string",
        "optional": false,
        "field": "op"
      },
      {
        "type": "int64",
        "optional": true,
        "field": "ts_ms"
      },
      {
        "type": "struct",
        "fields": [
          {
            "type": "string",
            "optional": false,
            "field": "id"
          },
          {
            "type": "int64",
            "optional": false,
            "field": "total_order"
          },
          {
            "type": "int64",
            "optional": false,
            "field": "data_collection_order"
          }
        ],
        "optional": true,
        "field": "transaction"
      }
    ],
    "optional": false,
    "name": "dbserver1.testdb.reservation.Envelope"
  },
  "payload": {
    "before": null,
    "after": {
      "reservation_id": "9fd18951-4f7b-42d2-8b5e-2601eaf48adf",
      "account_id": "123",
      "room_id": "999",
      "start_time": 1605011670000000,
      "end_time": 1607603850000000
    },
    "source": {
      "version": "1.9.4.Final",
      "connector": "mysql",
      "name": "dbserver1",
      "ts_ms": 1657368279000,
      "snapshot": "false",
      "db": "testdb",
      "sequence": null,
      "table": "reservation",
      "server_id": 1,
      "gtid": null,
      "file": "binlog.000056",
      "pos": 10447,
      "row": 0,
      "thread": 365,
      "query": null
    },
    "op": "c",
    "ts_ms": 1657368279907,
    "transaction": null
  }
}
```

해당 consumer을 통해 데이터의 변경 로그를 받아 볼 수 있으며 payload를 파싱하여 쿼리 전용 모델에 변경 사항을
반영할 수 있다.

앞선 장/단점에서도 언급된 것처럼 CQRS패턴은 꼭 필요한 경우에만 도입해야 하며 단순 CRUD의 경우 도입하는 비용이 더 많이 들어갈 수 있다.

CQRS 적용 코드 git: https://github.com/pjh37/cqrs_pattern/tree/master/src/main/java/pjh/cqrspattern <br>
환경 구성을 위한 docker-compose: https://github.com/pjh37/docker-images/blob/master/images/cdc/docker-compose.yml
