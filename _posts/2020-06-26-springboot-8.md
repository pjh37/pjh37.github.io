---
layout: post
title:  " spring boot jpa - 프록시와 연관관계 관리"
date:   2020-06-26
desc: "spring boot jpa 입문"
keywords: "springboot"
categories: [Springboot]
tags: [springboot,jpa]
icon: icon-html
---

프록시와 연관관계 관리
-----

<br/>

## 프록시 기초
+ ### find() : 데이터베이스를 통해서 실제 객체 조회
+ ### getReference() : 데이터베이스 조회를 미루는 가짜(프록시)

<br/>

## 프록시의 특징
<br/>
![프록시](https://user-images.githubusercontent.com/37110261/85830121-06e0eb80-b7c7-11ea-91c0-00fc51b5f5c5.PNG)
+ ### 프록시 객체는 처음 사용할때 한 번만 초기화
+ ### 프록시 객체를 초기화 할 때, 프록시 객체가 실제 엔티티로 바뀌는 것은 아니다, 초기화 되면 프록시 객체를 통해서 실제 엔티티에 접근가능
+ ### 프록시 객체는 원본 엔티티를 상속받음, 따라서 타입 체크시 주의해야한다.(==비교 실패 , 대신 instance of 사용할것)
+ ### 영속성 컨텍스트에 찾는 엔티티가 이미 있으면 getReference()를 호출해도 실제 엔티티 반환
+ ### 영속성 컨텍스트의 도움을 받을 수 없는 준영속 상태일때 프록시를 초기화하면 문제발생

<br/>



## 즉시로딩과 지연로딩(중요!!)
> Member 1 : N Team (1:N)관계일때
### Member를 조회할때 team 도 함께 조회해야하는가???

## 지연 로딩(LAZY)을 사용해서 프록시로 조회
### 단순히 member 정보만 사용하는 비지니스 로직에서는 지연로딩
``` java
@Entity
public class Member {
    @Id
    @GeneratedValue
    private Long id;

    @Column(name = "USERNAME")
    private String name;

    @ManyToOne(fetch = FetchType.LAZY) //**
    @JoinColumn(name = "TEAM_ID")
    private Team team;
    //..
}

```
<br/>
![지연로딩](https://user-images.githubusercontent.com/37110261/85845214-e15fdc00-b7de-11ea-8086-1e84101a6cb0.PNG)


<br/>

## Member와 Team을 자주 함계 사용한다면?
``` java
@Entity
public class Member {
    @Id
    @GeneratedValue
    private Long id;

    @Column(name = "USERNAME")
    private String name;

    @ManyToOne(fetch = FetchType.EAGER) //**
    @JoinColumn(name = "TEAM_ID")
    private Team team;
    //..
}

```
<br/>
![즉시로딩](https://user-images.githubusercontent.com/37110261/85845566-6b0fa980-b7df-11ea-8cb0-0f49fa768cf2.PNG)

<br/>

## 프록시와 즉시로딩 주의!!!!
+ ### 가급적 지연 로딩만 사용할것!!(실무에서 특히)
+ ### 즉시 로딩을 적용하면 예상하지 못한 SQL발생(연결된 수많은 테이블이 조인이 걸려 가져올 수도 있어서 성능 다운)
+ ### 즉시 로딩은 JPQL에서 N+1문제를 일으킨다.
+ ### @ManyToOne, @OneToOne은 default설정이 즉시 로딩 -> lazy로 바꾸자!!
+ ### @OneToMany, @ManyToMany는 기본이 지연 로딩



