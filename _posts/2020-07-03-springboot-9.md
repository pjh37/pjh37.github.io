---
layout: post
title:  " spring boot 프로젝트 생성"
date:   2020-06-26
desc: "spring boot 시작"
keywords: "springboot"
categories: [Springboot]
tags: [springboot,jpa]
icon: icon-html
---
## 기본 설정

<hr/>

## application.yml (프로젝트 디렉토리에 넣었음)
![application설정](https://user-images.githubusercontent.com/37110261/86456073-80934f00-bd5c-11ea-955a-46602d113e60.PNG)

## build.gradle 설정
### spring jpa, security, lombok은 개발및 편의를 위해 넣었습니다.

> 아래것만 있어도 기본적인 개발은 가능합니다.
``` java
compile('org.springframework.boot:spring-boot-starter-web')
```
![13](https://user-images.githubusercontent.com/37110261/86456081-82f5a900-bd5c-11ea-9af1-ce7d080101ee.PNG)

<br/>

### jpa, security 추가

![jpa_security](https://user-images.githubusercontent.com/37110261/86456085-8426d600-bd5c-11ea-8f55-6790eb9a64b3.PNG)

<br/>

### 생성자, Getter, Setter을 편하게 생성 시켜주는 lombok 추가
![lombok](https://user-images.githubusercontent.com/37110261/86456089-85580300-bd5c-11ea-8891-e05461ffebae.PNG)

<br/>

### 테스트 관련 의존성 추가
![test](https://user-images.githubusercontent.com/37110261/86456100-87ba5d00-bd5c-11ea-84b2-153241e08db3.PNG)