---
layout: post
title:  " spring boot jpa - jpa란 무엇이며 왜 사용하는가"
date:   2020-06-17
desc: "spring boot jpa 입문"
keywords: "springboot"
categories: [Springboot]
tags: [springboot,jpa]
icon: icon-html
---

JPA란 무엇이고 왜 사용하는가?
-----

<hr/>


## JPA?
### - JAVA Persistence API
### - 자바 진영의 ORM기술 표준

<br/>
## ORM(Object-relational mapping)
> 객체는 객체대로 설계  
> 관계형 데이터베이스는 관계형 데이터베이스대로 설계  
> ORM 프레임워크가 중간에서 매핑  
> 대중적인 언어에는 대부분 ORM 기술이 존재  

<br/>

## JPA는 애플리케이션과 JDBC 사이에서 동작
![JPA](https://user-images.githubusercontent.com/37110261/84868933-58e38c00-b0b8-11ea-87e4-42dcd6a67ecb.PNG)

<br/>

## JPA를 사용하는 이유
- ### SQL중심적인 개발에서 객체 중심으로 개발
- ### 생산성
- ### 유지보수
- ### 패러다임 불일치 해결
- ### 성능
- ### 데이터 접근 추상화와 벤더 독립성
- ### 표준

<br/>

SQL 중심적인 개발의 문제점
====

객체와 관계형 데이터베이스의 차이
----
+ ### 상속
    + ### 객체에는 상속관계가 있지만 RDB는 없다.(유사한게 있기는 하지만)
    + ### 객체상속관계와 유사한 물리모델 Table 슈퍼타입-서브타입관계가 있다.
    + ### Table 슈퍼타입-서브타입을 사용하면 각각의 테이블에 따른 조인 SQL작성과 각각의 객체생성으로 인해 복잡해진다. 그래서 DB에 저장할 객체는 상속관계를 안쓴다.
+ ### 연관관계
    + ### 객체는 참조를 통해 가져옴, RDB는 Pk,FK등으로 조인을 통해 가져온다.

+ ### 객체는 자유롭게 객체그래프를 탐색할 수 있어야한다. 그에반해 db는 처음 실행하는 SQL에 따라 탐색범위가 정해지게 된다. 이로인해 엔티티의 신뢰문제가 발생할 수 있다. 

``` java
class MemberService{
    public void precess(){
        Member member=memberDAO.find(memberId);
        member.getTeam();//???
        member.getOrder().getDelivery();// ???
    }
}

// . 을 이용해 탐색이 되는지 코드를 뜯어봐야 알 수 있다.
```

> 객체를 테이블에 맞춰서 모델링하게된다.

+ ### 모든 객체를 미리 로딩할 수는 없다.

> 객체답게 모델링 할수록 매핑 작업만 늘어난다.
> 객체를 자바 컬렉션에 저장하듯이 DB에 저장할 수는 없을까?
> 라는 의문을 해소해주는 것이 JPA ORM이다.

<br/>

## 생산성(jpa & CRUD)
+ ### 저장 : jpa.save(member)
+ ### 조회 : jpa.find(memberId)
+ ### 수정 : member.setName("변경이름")
+ ### 삭제 : jpa.delete(member)

<br/>

## 유지보수 기존 : 필드변경시 모든 SQL을 수정

``` java
public class Member{
    private String memberId;
    private String name;
    private String tel;// 추가되는 항목
}
```
``` sql
Insert into member(member_ID, NAME,TEL) values // tel 추가
select member_ID, NAME,TEL from member //tel 추가
update member set ... tel=? //tel추가
```
<br/>

``` java
public class Member{
    private String memberId;
    private String name;
    private String tel;// 추가되는 항목
}
```
> JPA를 사용할 경우 필드만 추가하면 된다.

<br/>

## JPA에서 비교
``` java
String memberId="111"
Member member1=jpa.find(Member.class,memberId);
Member member2=jpa.find(Member.class,memberId);
member1==member2
```
> 동일한 트랜잭션에서 조회한 엔티티는 같음을 보장한다.

<br/>

## JPA의 성능 최적화 기능
### 1. 1차 캐시와 동일성 보장
### 2. 트랜잭션을 지원하는 쓰기 지연
### 3. 지연로딩(Lazy loading)

<br/>
### 1차 캐시와 동일성 보장
``` java
String memberId="111"
Member member1=jpa.find(Member.class,memberId);// sql
Member member2=jpa.find(Member.class,memberId);// 캐시
member1==member2//true
```
<br/>
### 트랜잭션을 지원하는 쓰기 지연
#### 1. 트랜잭션을 커밋할때까지 INSERT SQL을 모은다
#### 2. JDBC BATCH SQL 기능을 사용해서 한번에 SQL 전송

#### insert
``` java
transaction.begin();

jpa.save(member1);
jpa.save(member2);
jpa.save(member3);
//insert sql을 데이터베이스에 보내지 않는다.

transaction.commit();
//커밋하는 순간 데이터베이스에 insert sql을 모아서 보낸다.
```

<br/>
#### update
#### 1. UPDATE,DELETE로 인한 row락 시간 최소화
#### 2. 트랜잭션 커밋 시 UPDATE,DELETE SQL 실행하고 바로 커밋
``` java
transaction.begin();

jpa.UPDATE(member1);
jpa.DELETE(member2);
비지니스_로직_실행();
//비지니스 로직 수행동안 db로우 락이 걸리지 않는다.

transaction.commit();
//커밋하는 순간 데이터베이스에 UPDATE,DELETE sql을 모아서 보낸다.
```

<br/>

## 지연 로딩과 즉시 로딩
### + 지연 로딩 : 객체가 실제 사용될 때 로딩
### + 즉시 로딩 : JOIN SQL로 한번에 연관된 객체까지 미리 조회

``` java
//지연 로딩
Member member=memberDAO.find(memberId); // select * from member
Team team=member.getTeam();
String teamName=team.getName(); // select * from team
```

<br/>

``` java
//즉시 로딩
Member member=memberDAO.find(memberId); // select M.*,T.* from member join team ... 
Team team=member.getTeam();
String teamName=team.getName(); 
```
<br/>

> 연관된 객체를 가끔씩만 불러올때는 지연 로딩이 좋다.  
> 연관된 객체를 항상 계속 사용해야 할 경우 즉시로딩으로 한번에 가져오는게 성능상 더 좋다.