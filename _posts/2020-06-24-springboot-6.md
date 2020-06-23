---
layout: post
title:  " spring boot jpa - 엔티티 매핑"
date:   2020-06-23
desc: "spring boot jpa 입문"
keywords: "springboot"
categories: [Springboot]
tags: [springboot]
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