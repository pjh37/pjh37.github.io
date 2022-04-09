---
layout: post
title:  " [Java] 책임-연쇄(chain-of-responsibility) 패턴"
date:   2022-04-09
desc: "Filter,intercepter에서 볼 수 있는 책임-연쇄 패턴"
keywords: "DesignPattern"
categories: [Designpattern]
tags: [designpattern]
icon: icon-html
---

## 책임-연쇄(chain-of-responsibility) 패턴

```
요청을 보내는쪽(sender)과 요청을 처리하는 쪽(receiver)을 분리하는 패턴
```

+ 핸들러 체인을 사용해서 요청을 처리한다.

![책임연쇄-1](https://user-images.githubusercontent.com/37110261/162557966-daaf0c94-7e99-4120-87ca-1e4541708aab.PNG)


<br/>

## 구현

### 구현 상세
+ 인증-> 로깅 -> 암호화 순으로 필터링 필요
+ RequsetHandler 추상 클래스 구현(nextHandler를 받아줄 필드 필요로 인터페이스가 아닌 추상 클래스 사용)
+ 해당 추상클래스를 상속 받는 인증,로깅,암호화 클래스 구현
+ 인증,로깅,암호화의 구현 클래스들은 생성자로 넘어오는 RequestHandler들을 super를 이용해 부모 생성자로 pass


## RequestHandler

``` java
public abstract class RequestHandler {
    private RequestHandler nextHandler;

    public RequestHandler(RequestHandler nextHandler) {
        this.nextHandler = nextHandler;
    }

    public void handle(Request request){
        if(nextHandler!=null){
            nextHandler.handle(request);
        }
    }
}
```

<br/>

## AuthRequestHandler

<br/>

``` java
public class AuthRequestHandler extends RequestHandler {
    public AuthRequestHandler(RequestHandler nextHandler) {
        super(nextHandler);
    }

    @Override
    public void handle(Request request) {
        System.out.println("인증처리를 합니다.");
        super.handle(request);
    }
}
```

<br/>

## LoggingRequestHandler

<br/>

``` java
public class LoggingRequestHandler extends RequestHandler {
    public LoggingRequestHandler(RequestHandler nextHandler) {
        super(nextHandler);
    }

    @Override
    public void handle(Request request) {
        System.out.println("로깅 합니다.");
        super.handle(request);
    }
}
```

<br/>

## EncryptRequestHandler

<br/>

``` java
public class EncryptRequestHandler extends RequestHandler {
    public EncryptRequestHandler(RequestHandler nextHandler) {
        super(nextHandler);
    }

    @Override
    public void handle(Request request) {
        System.out.println("암호화되었습니다.");
        super.handle(request);
    }
}
```

<br/>

## Client

``` java
public class Client {
    private RequestHandler requestHandler;

    public Client(RequestHandler requestHandler) {
        this.requestHandler = requestHandler;
    }

    public void doWork(){
        Request request=new Request("Request ID: xrt324%!tio");
        requestHandler.handle(request);
    }
}

public class App {
    public static void main(String[] args) {
        RequestHandler chain=new AuthRequestHandler(new LoggingRequestHandler(new EncryptRequestHandler(null)));
        Client client=new Client(chain);
        client.doWork();
    }
}
```

<br/>

## 동작 순서
1. 만들어진 RequestHandler객체에 처리하고 싶은 순서대로 구현 객체들을 넣은 체인을 만든다.
    + 만들어진 체인의 동작은 다음과 같다. AuthRequestHandler의 상위 클래스의 nextHandler에 LoggingRequestHandler가 등록되고 LoggingRequestHandler의 상위 클래스의 nextHandler에 EncryptRequestHandler가 등록된다.
2. handle 메서드가 호출될때는 super.handle(request) 가 실행되는데 이 코드의 의미는 자신의 상위 클래스에 등록된 핸들러의 재정의 된 handle이 실행된다.

<br/>

## 결과
![책임-연쇄-2](https://user-images.githubusercontent.com/37110261/162559134-c8b6ade1-2023-482b-8882-325e081adf28.PNG)

<br/>

### 사용 되는 곳
+ spring에서 doFilter, security 인증관련 필터를 걸때 이용된다.
