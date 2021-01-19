---
layout: post
title:  " [Java] 싱글턴 패턴"
date:   2020-08-12
desc: " Singleton pattern"
keywords: "DesignPattern"
categories: [Designpattern]
tags: [designpattern]
icon: icon-html
---

Singleton 패턴이란
------

<br/>

## 정의
### 생성자가 여러번 호출되도 실제로 생성되는 객체는 1개이고 최초 생성 이후에
### 호출된 생성자는 최초의 생성자가 생성한 객체를 리턴한다. 이러한 디자인 유형을
### 싱글턴 패턴이라고 한다. 주로 공통된 객체를 여러개 생성해서 사용하는 DBCP(Database Connection pool)
### 과 같은 상황에서 많이 사용한다.

<br/>

## 싱글톤 단점
### 멀티 쓰레딩 환경에서 동기화 문제가 발생할 수 있다.
### 싱글톤 인스턴스가 너무 많은 일을 하게 되거나 많은 데이터를 보유할 경우 다른 클래스의 인스턴스간에 결합도가 높아져
### 객체지향 설계 원칙(개방-폐쇄)을 어기게 된다.


<br/>

## 기본 구현

<br/>

``` java
public class Printer{
    private Printer printer=null;

    private Printer(){}

    public static Printer getInstence(){
        if(printer==null){
            printer=new Printer();
        }
        return printer;
    }

    public void print(String str){
        System.out.println(str);
    }
}
```

<br/>

### 동기화 문제 해결책
+ ### 정적 변수에 인스턴스를 만들어 바로 초기화 하는 방법
+ ### 인스턴스를 만드는 메서드에 동기화 하는 방법

<br/>

## 정적 변수에 인스턴스를 만들어 바로 초기화

``` java
public class Printer{
    private static Printer printer=new Printer();

    private Printer(){}

    public static Printer getInstence(){
        return printer;
    }

    public void print(String str){
        System.out.println(str);
    }
}
```

### 정적 변수는 객체가 생성되기 전 클래스가 메모리에 로딩될때 만들어져 초기화가 한 번만 실행된다.
### 또한 정적 변수는 프로그램이 시작된때부터 종료될때까지 메모리에 계속 상주하면서 클래스에서 생성된 모든
### 객체에서 접근할 수 있다.
### 이 방법은 다중 스레드 환경에서 문제를 발생시켰던 if(printer==null) 이라는 조건 구문을 제거하기 위한 방법이다.

<br/>

## 인스턴스를 만드는 메서드에 동기화 하는 방법

``` java
public class Printer{
    private Printer printer=null;

    private Printer(){}

    public synchronized static Printer getInstence(){
        if(printer==null){
            printer=new Prineter();
        }
        return printer;
    }

    public void print(String str){
        System.out.println(str);
    }
}
```

### 이러한 synchronized 방식은 동기화문제를 해결하는 대신 성능저하를 발생시킨다.

<br/>

## 싱글톤 패턴 vs 정적 클래스
### 굳이 싱글톤 패턴을 사용하지 않고 정적 메서드로만 이루어진 정적 클래스를 사용해도 동일한 효과를 얻을 수 있다.

<br/>

``` java
public class Printer{
    private static int counter=0;
    public synchronized static void print(String str){
        counter++;
        System.out.print(str+counter);
    }
}
```

<br/>

### 정적 클래스를 이용하는 방법이 싱글턴 패턴을 이용하는 방법과 가장 다른 점은 객체를 전혀 생성하지 않고 메서드를 사용한다는 점이다.
### 그리고 정적 메서드를 사용하므로 일반적으로 실행될 때 바인딩(컴파일 타임에) 되는 인스턴스 메서드를 사용하는 것보다 성능면에서 우수하다고 볼 수 있다.
### 그러나 정적 클래스를 사용할 수 없는 경우가 있다. 가장 대표적인 경우가 인터페이스를 ㄱ 현 해야 하는 경우다. 정적 메서드는 인터페이스에서 사용할 수 없다.

<br/>

### 인터페이스를 사용하는 주된 이유는 대체 구현이 필요한 경우다. 이는 특히 모의 객체를 이용해 단위 테스트를 수행할 때 매우 중요하다.