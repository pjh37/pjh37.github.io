---
layout: post
title:  " spring boot jpa - 연관관계 매핑"
date:   2020-06-23
desc: "spring boot jpa 입문"
keywords: "springboot"
categories: [Springboot]
tags: [springboot,jpa]
icon: icon-html
---

JPA의 연관관계 매핑
-----

<hr/>

## 예제 시나리오
+ ### 회원과 팀이 있다.
+ ### 회원은 하나의 팀에만 소속될 수 있다.
+ ### 회원과 팀은 다대일 관계다.

<br/>
## 단방향 매핑

<br/>
## Member
``` java
@Entity
@Getter
@Setter
public class Member {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    @Column(name="MEMBER_ID")
    private Long id;
    private String name;

    @ManyToOne
    @JoinColumn(name="TEAM_ID")
    private Team team;
}
```

<br/>
## Team
``` java
@Entity
@Getter
@Setter
public class Team {
    @Id
    @GeneratedValue
    private Long id;

    private String name;
}
```

<br/>
### 사용할때
``` java
Team team=new Team();
team.setName("teamA");

//멤버에 팀을 id가 아니라 team자체를 set할수 있다.

Member member1=new Member();
member1.setName("홍길동");
member1.setTeam(team);

Member member2=new Member();
member2.setName("둘리");
member2.setTeam(team);
```

<br/>

## 양방향 매핑(member는 그대로 사용하고 team만 바꾸면됨)
### Team
``` java
@Entity
@Getter
@Setter
public class Team {
    @Id
    @GeneratedValue
    private Long id;

    private String name;

    //mappedBy의 이름은 Member에  @JoinColum(name="TEAM_ID") 어노테이션이 붙어있는 변수의 변수name이다.
    @OneToMany(mappedBy = "team")
    List<Member> members=new ArrayList<>();
}
```

<br/>

## 다대다 매핑
+ ### N:M관계에 있는 테이블간의 매핑은 @ManyToMany 쓰지말고 중간 연결 테이블을 하나더 만들어 매핑해야 한다.
> @ManyToMany를 사용해서 매핑할경우 중간테이블을 jpa가 지멋대로 만들어 버린다.
+ ### 현업에서는 중간테이블이 단순하지 않음으로 사용자가 중간테이블을 만들어 컨트롤 해야한다.








## 연관관계 매핑의 주인
+ ### FK가 있는 쪽이 연관관계 매핑의 주인으로 생각하자
