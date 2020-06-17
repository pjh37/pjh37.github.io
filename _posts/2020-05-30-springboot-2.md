---
layout: post
title:  " spring입문 IOC, DI -1"
date:   2020-05-30
desc: "spring inversion of control, Dependency Injection"
keywords: "springboot"
categories: [Springboot]
tags: [springboot]
icon: icon-html
---

Inversion of control
=====

### 의존성에 대한 컨트롤이 뒤바꼈다.
### 원래는 아래와 같은 코드 처럼 자신이 만들었다
<br/>

``` java
class OwnerController{
    private OwnerRespository repository=new OwnerRespository();
}

```
<br/>
### 하지만 여기서 IOC가 될 경우 아래와 같이 쓸 수 있다.
<br/>
``` java
class OwnerController{
    private OwnerRespository repository;

    // 의존성을 주입해준다.
    public OwnerController(OwnerRespository repository){
        this.repository=repository;
    }
    
}

```
<br/>

### 예전에는 자바의 객체를 생성하고 객체간의 의존관계를 연결하는 등의 제어권을 개발자가 가지고 있었지만
### 스프링에서는 제어권을 생명주기를 관리하는 컨테이너가 가져가는데 이것을 제어권 역전 IOC라고 한다.

<br/>

IOC Container   
=====
<br/>

### - 빈(bean)을 만들고 엮어주며 제공해준다. 즉, 컨테이너 안에 있는 빈들의 의존성을 관리한다.
### - 기본적으로 컨테이너는 객체를 생성하고 객체간의 의존성을 이어주는 역할을 한다.
<br/>

## 1. BeanFactory
### BeanFactory인터페이스는 IoC 컨테이너의 기능을 정의하고 있는 인터페이스 이며, Bean의 생성 및 의존성 주입, 생명주기관리등의 기능을 제공한다
> 여기서 bean이란 IoC 컨테이너에 의해 생성되고 관리되는 객체를 의미한다.

<br/>
## 2. ApplicationContext
### BeanFactory 인터페이스를 상속받는 ApplicationContext는 BeanFactory가 제공하는 기능 외에 AOP, 메세지처리, 이벤트 처리 등의 기능을 제공한다.


<br/>

빈(Bean)
=====

> 스프링 IoC 컨테이너가 관리하는 객체

<br/>

## 빈 등록 방법
+ ### Component Scanning
    - ### @Component
        - ### @Repository
        - ### @Service
        - ### @Controller   

<br/>

> Component Scanning 할때 SpringBootApplication이라는 어노테이션이 붙은 하위의 패키지, 클래스들을 scan하여
> @Component라는 어노테이션이 붙은 클래스들을 빈으로 등록해준다.
> @Repository,@Service,@Controller 는 왜 빈으로 등록되지? -> 어노테이션 내부 코드를 보면 @Component가 붙어있다.

+ ### 직접 xml이나 자바 설정 파일에 등록
``` java
@Bean
public String nameById(){
    return "name";
}
```
> 주의점: @Bean 어노테이션으로 빈을 등록할때는 @Configuration 어노테이션이 붙은 클래스 안에서 작성해야한다.

<br/>

## 빈 꺼내 쓰는 방법
### @Autowired 또는 @Inject
### ApplicationContext에서 getBean()으로 직접 꺼내기

``` java
@Autowired
String nameById;

```

<br/>

의존성 주입 (Dependency Injection -> DI라고 부름)
=====

## 일반적으로 @Autowired, @Inject를 붙힌다 어디에?
+ ### 생성자
+ ### 필드
+ ### Setter

## 어노테이션이 안붙었을 경우에는 어떻게??
### Bean이 되는 클래스에 생성자가 오직 하나만 있고 그 생성자의 매개변수가 빈으로 등록이 되어 있다면
### 그 Bean을 주입해준다.