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

## 구현

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