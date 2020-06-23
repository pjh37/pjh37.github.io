---
layout: post
title:  " spring boot jpa - 영속성 컨텍스트와 플러시"
date:   2020-06-23
desc: "spring boot jpa 입문"
keywords: "springboot"
categories: [Springboot]
tags: [springboot]
icon: icon-html
---

JPA의 영속성 컨텍스트와 플러시
-----

<hr/>

## 엔티티 매니저 팩토리와 엔티티 매니저

<br/>

![JPA](https://user-images.githubusercontent.com/37110261/85383676-34d1f000-b57b-11ea-8244-6a6ea99831fa.PNG)
#####  자바 ORM 표준 JPA그림에서 가져옴

<br/>

## 영속성 컨텍스트
### - 엔티티를 영구 저장하는 환경
### - 영속성 컨텍스트는 논리적 개념으로 눈에 보이지 않는다.
### - 엔티티 매니저를 통해서 영속성 컨텍스트에 접근

<br/>

## 엔티티의 생명주기
### - 비영속(new/transiend) -> 영속성 컨텍스트와 전혀 관계가없는 새로운 상태
### - 영속(managed) -> 영속성 컨텍스트에 관리되는 상태
### - 준영속(detached) -> 영속성 컨텍스트에 저장되었다가 분리된 상태
### - 삭제(removed) -> 삭제된 상태

<br/>

![생명주기그림](https://user-images.githubusercontent.com/37110261/85384009-a7db6680-b57b-11ea-8a1f-004ee7fc6055.PNG)

<br/>

## 비영속
<br/>
![비영속](https://user-images.githubusercontent.com/37110261/85385592-72d01380-b57d-11ea-99ac-372a4c7624dd.PNG)
``` java
//객체를 단순히 생성만 했을때 
Member member = new Member("member1","회원1");
```

<br/>

## 영속
<br/>
![영속](https://user-images.githubusercontent.com/37110261/85386906-21288880-b57f-11ea-8aa6-659e1306ca90.PNG)
``` java
Member member = new Member("member1","회원1");

EntityManagerFactory emf=Persistence.createEntityManagerFactory('unit name');
EntityManager em=emf.createEntityManager();
em.getTransaction().begin();

//객체 저장(영속)
em.persist(member);
```
<br/>

## 준영속,삭제
``` java
//회원 엔티티를 영속성 컨텍스트에서 분리, 준영속 상태
em.detach(member);

//객체를 삭제한 상태(삭제)
em.remove(member);
```
<br/>

## 영속성 컨텍스트의 이점
+ ### 1차캐시
+ ### 동일성 보장
+ ### 트랜잭션을 지원하는 쓰기 지연
+ ### 변경감지
+ ### 지연로딩(lazy loading)

<br/>

## 1차 캐시에서 조회
### 1차캐시에서 먼저 조회한뒤 없으면 DB에서 조회한다.
<br/>

![1차캐시](https://user-images.githubusercontent.com/37110261/85389271-2dfaab80-b582-11ea-9e6a-09aa82fdf378.PNG)

``` java
Member member = new Member("member1","회원1");

//1차 캐시에 저장된다.
em.persist(member);

//1차캐시에서 조회
Member findMember=em.find(Member.class"member1");

```

<br/>

## 트랜잭션을 지원하는 쓰기 지연



``` java
EntityManager em=emf.createEntityManager();
EntityTransaction transaction = em.getTransaction();

//엔티티 매니저는 데이터 변경시 트랜잭션을 시작해야 한다.
transaction.begin();//트랜잭션 시작

em.persist(memberA);
em.persist(memberB);

// 여기까지 insert을 데이터베이스에 보내지 않는다.

//커밋하는 순간 데이터베이스에 insert sql 을 보낸다.
transaction.commit();//커밋
```

<br/>
![쓰기지연](https://user-images.githubusercontent.com/37110261/85390365-b3329000-b583-11ea-8b23-552216de0e5a.PNG)

<br/>

## 변경 감지



``` java
EntityManager em=emf.createEntityManager();
EntityTransaction transaction = em.getTransaction();

transaction.begin();//트랜잭션 시작

//영속 엔티티 조회 
Member memberA=em.find(Member.class,"memberA");

//영속 엔티티 데이터 수정
memberA.setUsername("aaa");
memberA.setAge(19);

//em.update(member) <= 이런 코드가 있어야 하는가????

transaction.commit();//커밋
```
<br/>

### em.update(member) 필요업다.
### 스냅샷을 찍어 놓고 변경을 감지하여 영속성 컨텍스트에서 업데이트 쿼리를 쓰기 지연 저장소에 넣어 놓고
### 플러시를 하여 업데이트를 데이터베이스에 반영한다.

<br/>
![변경감지](https://user-images.githubusercontent.com/37110261/85392135-16bdbd00-b586-11ea-89fe-d5020fabd927.PNG)

<br/>
## 플러시
> 영속성 컨텍스트의 변경내용을 데이터베이스에 반영

<br/>

### 플러시 발생
+ ### 변경 감지
+ ### 수정된 엔티티 쓰기 지연 sql저장소에 등록
+ ### 쓰기 지연 sql 저장소의 쿼리(등록,수정,삭제 쿼리)를 데이터베이스에 전송

<br/>

## 영속성 컨텍스트를 플러시하는 방법
+ ### em.flush() -> 직접 호출
+ ### 트랜잭션 commit -> 플러시 자동 호출
+ ### JPQL 쿼리 실행 -> 플러시 자동 호출

<br/>

## 플러시 주의할 특징
+ ### 영속성 컨텍스트를 비우지 않는다.
+ ### 영속성 컨텍스트의 변경내용을 데이터베이스에 동기화한다.
+ ### 트랜잭션이라는 작업단위가 중요하다 -> 커밋 직전에만 동기화 하면 됨


<br/>

## 준영속 상태
+ ### 영속 상태의 엔티티가 영속성 컨텍스트에서 분리(detached)
+ ### 영속성 컨텍스트가 제공하는 기능을 사용 못함