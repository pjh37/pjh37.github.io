---
layout: post
title:  " spring입문 AOP -1"
date:   2020-05-30
desc: "spring Aspect Oriented Programming"
keywords: "springboot"
categories: [Springboot]
tags: [springboot]
icon: icon-html
---

AOP
=====
<br/>

## 흩어진 코드를 한곳으로 모으는 코딩 기법이다!!
### 객체지향의 기본원칙을 적용해도 핵심기능에서 부가기능을 분리해서 모듈화하는 것은 어렵다
### AOP는 핵심적인 기능에서 부가기능을 분리한다.
### 분리한 부가기능을 Aspect라는 모듈형태로 만들어서 설계하고 개발하는 방법이다.
### 어떻게?  주로 proxy패턴을 사용한다.

<br/>

## 예시로 메소드의 실행시간을 측정하는 어노테이션만들어 보자

<br/>

## @LogExecutionTime 어노테이션 (어디에 적용할지 표시해두는 용도)
``` java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface LogExecutionTime{
}
```

<br/>

## 실제 Aspect(@LogExecutionTime 어노테이션 달린곳에 적용)

``` java
@Component
@Aspect
public class LogAspect{
    Logger logger=LoggerFactory.getLogger(LogAspect.class);

    @Around("@annotation(LogExecutionTime)")//어디에 적용할지 범위 설정
    public Object logExecutionTime(ProceedingJoinPoint joinPoint)throws Throwable{
        StopWatch stopWatch=new StopWatch();//spring 에서 제공하는 스톱워치
        stopWatch.start();

        Object proceed=joinPoint.proceeod();

        stopWatch.stop();
        logger.info(stopWatch.prettyPrint());
        return proceed;
    }
}
```

## 사용할때는 메소드 등에 어노테이션을 달면 로그에 함수의 작동시간이 출력되게 된다.

``` java
@LogExecutionTime
public String getMax(){
    ....
}
```
