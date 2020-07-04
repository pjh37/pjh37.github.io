---
layout: post
title:  " spring boot jpa - 엔티티 매핑"
date:   2020-06-23
desc: "spring boot jpa 입문"
keywords: "springboot"
categories: [Springboot]
tags: [springboot,jpa]
icon: icon-html
---

JPA의 엔티티 매핑
-----

<hr/>

## @Entity
+ ### @Entity가 붙은 클래스는 JPA가 관리
+ ### JPA를 사용해서 테이블과 매핑할 클래슨는 @Entity필수

> 주의!!    
> 기본 생성자 필수(파라미터가 없는 public 또는 protected 생성자)  
> final 클래스, enum, interface, inner 클래스 사용 X  
> 저장할 필드에 final 사용 X  

## 속성 : name (별로 사용할일 없음 왠만하면 기본값쓰자 햇갈리니까!!!!)
+ ### JPA에서 사용할 엔티티 이름을 지정한다
+ ### 기본값: 클래스이름을 그대로 사용
+ ### 같은 클래스 이름이 없으면 가급적 기본값을 사용한다!!!!!!


<br/>

## 데이터베이스 스키마 자동 생성 - 주의
+ ### 운영장비에는 절대 create, create-drop, update 사용하면 안됨!!!!
+ ### 개발 초기 단계는 create 또는 update
+ ### 테스트 서버는 update 또는 validate
+ ### 스테이징과 운영 서버는 validate 또는 none

<br/>
## 기본 키 매핑
@Id
@GeneratedValue

``` java
@Id
@GeneratedValue(strategy = GenerationType.AUTO)
private Long id;

```

<br/>

## IDENTITY 전략(GenerationType.IDENTITY)
+ ### 기본 키 생성을 데이터베이스에 위임
+ ### 주로 MYSQL, PostgreSQL, SQL Server, DB2에서 사용
+ ### JPA는 보통 트랜잭션 커밋 시점에 INSERT SQL 실행
+ ### AUTO_INCREMENT는 데이터베이스에 INSERT SQL을 실행한 이후 ID값을 알 수 있다!!! <- 중요
+ ### IDENTITY 전략은 em.persist()시점에 즉시 ISNERT SQL실행하고 DB에서 식별자를 조회한다.

<br/>

## TABLE 전략 (현업에서 잘 사용 안함!)
+ ### 키 생성 전용 테이블을 하나 만들어서 데이터베이스 시퀸스를 흉내냄
+ ### 장점 : 모든 데이터베이스에 적용가능
+ ### 단점 : 성능

<br/>

## 권장하는 식별자 전략
+ ### 기본키 제약 조건 : not null, 유일, 변하면 안됨
+ ### 미래까지 이 조건을 만족하는 자연키는 찾기 어렵다. 대리키(대체키)를 사용하자
+ ### 주민등록번호는 기본 키로 적절하지 않다.
+ ### 권장 : Long형 + 대체키 + 키 생성전략 사용