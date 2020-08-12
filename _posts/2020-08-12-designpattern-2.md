---
layout: post
title:  " [Java] Builder 패턴"
date:   2020-08-12
desc: " Builder pattern"
keywords: "DesignPattern"
categories: [Designpattern]
tags: [designpattern]
icon: icon-html
---

Builder 패턴이란
------

<br/>

## 사용이유

+ ### 빌더 패턴객체의 생성 알고리즘과 조립방법을 분리 하는것이 목적
+ ### 코드읽기/유지보수가 편해진다. (가독성의 향상된다는 점이 중요하다)
+ ### 생성자의 인수가 많을때 모든 종류의 생성자를 만들 수 없기 때문에 빌더 패턴을 이용한다.

<br/>

## 구현

<br/>

``` java
public class Order {
    private String name;
    private String address;
    private int price;
    private int stock;

    public Order(){}

    public Order(OrderBuilder orderBuilder){
        this.name=orderBuilder.name;
        this.address=orderBuilder.address;
        this.price=orderBuilder.price;
        this.stock=orderBuilder.stock;
    }

    public static OrderBuilder builder(){
        return new OrderBuilder();
    }

    public static class OrderBuilder{
        private String name;
        private String address;
        private int price;
        private int stock;

        public OrderBuilder name(String name){
            this.name=name;
            return this;
        }

        public OrderBuilder address(String address){
            this.address=address;
            return this;
        }

        public OrderBuilder price(int price){
            this.price=price;
            return this;
        }

        public OrderBuilder stock(int stock){
            this.stock=stock;
            return this;
        }

        public Order build(){
            return new Order(this);
        }
    }

    public String toString(){
        return "주문제품명 : "+name+" " +
                "가격 : "+price+" " +
                "주소 : "+address+" "+
                "수량 : "+stock;
    }

}

public class Main {
    public static void main(String[] args){
        Order order=Order.builder()
                .name("홍길동")
                .address("서울")
                .price(1000)
                .stock(10)
                .build();
        System.out.println(order.toString());
    }
}
```

## 결과
> 주문제품명 : 홍길동 가격 : 1000 주소 : 서울 수량 : 10