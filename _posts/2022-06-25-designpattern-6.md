---
layout: post
title:  " [Java] 프록시(Proxy) 패턴"
date:   2022-06-25
desc: "접근 제한,캐싱을 할 수 있는 프록시 패턴"
keywords: "DesignPattern"
categories: [Designpattern]
tags: [designpattern]
icon: icon-html
---

## 프록시(Proxy) 패턴

### 클라이언트와 서버

![화면 캡처 2022-06-25 104113](https://user-images.githubusercontent.com/37110261/175753673-a860d4b0-4e08-48c0-b36d-4a3e729eca07.png)

+ Client: 서버에 필요한 것을 요청
+ Server: 서버는 클라이언트의 요청을 처리

<br/>

### 직접 호출과 간접 호출
![화면 캡처 2022-06-25 104839](https://user-images.githubusercontent.com/37110261/175753744-1456545d-59b0-4f21-9f16-b4203eb79e6b.png)

클라이언트와 서버개념에서 일반적으로 클라이언트가 서버를 직접 호출하고 처리 결과를 받는다.

![화면 캡처 2022-06-25 105032](https://user-images.githubusercontent.com/37110261/175753794-6ddebfb9-7237-488a-b67f-8a41da177693.png)

클라이언트가 요청한 결과를 서버에 직접 요청하는게 아니라 대리자를 통해서 간접적으로 서버에 요청할 수 있다.
여기서 대리자를 프록시(Proxy)라고 한다.

<b>프록시는 체인을 걸어 여러개를 적용할 수도 있다.</b>

![화면 캡처 2022-06-25 105317](https://user-images.githubusercontent.com/37110261/175753867-2ea4c205-d874-4390-8526-4e72d9a4d685.png)

<br/>

### 대체 가능

객체에서 프록시가 되기위해선 클라이언트가 서버에게 요청한 것인지 프록시에게 요청한 것인지 몰라야 된다.
그러므로 서버와 프록시는 같은 인터페이스를 사용해야 한다.
<br/>
그러면 클라이언트가 사용하는 서버객체를 프록시 객체로 변경해도 클라이언트 코드를 변경하지 않고 동작할 수 있게 된다.

![화면 캡처 2022-06-25 110030](https://user-images.githubusercontent.com/37110261/175754067-9e0962a3-c5c6-4558-8337-0e08aaec2a8b.png)

서버와 프록시가 같은 인터페이스를 사용하므로 DI를 이용해 대체 가능하다.

![화면 캡처 2022-06-25 110452](https://user-images.githubusercontent.com/37110261/175754188-0af4d39b-dbea-4531-8fc2-b53ef4072a0f.png)

### 프록시 주요 기능
프록시를 통해서 할 수 있는 일은 2가지가 있다.

+ 접근 제어
  + 권한에 따른 접근 차단
  + 캐싱
  + 지연 로딩
+ 부가 기능 추가
  + 원래 서버가 제공하는 기능에 더해서 부가 기능을 수행한다.
  + ex)요청 값이나,응답 값을 중간에 변형한다.
  + ex)실행 시간을 측정해서 추가 로그를 남긴다.

프록시 객체가 중간에 있으면 크게 <b>접근제어</b>와 <b>부가 기능 </b>추가를 수행할 수 있다.

<br/>


### GOF 디자인 패턴
같은 프록시를 사용하는 방법이지만 GOF 디자인 패턴에서는 <b>의도</b>에 따라서 프록시 패턴과
데코레이터 패턴으로 구분한다.

+ 프록시 패턴: 접근 제어가 목적
+ 데코레이터 패턴: 새로운 기능 추가가 목적

<br/>

### 프록시 패턴 적용 예제

![화면 캡처 2022-06-25 111921](https://user-images.githubusercontent.com/37110261/175754578-c6aecc34-71c1-47cf-8b99-f325a689ff5b.png)

![화면 캡처 2022-06-25 111902](https://user-images.githubusercontent.com/37110261/175754929-ec2c2511-5174-4c54-afa8-e2d748d64b7e.png)

<br/>

<b>Subject 인터페이스 </b>

```java
public interface Subject {
    String operation();
}

```

<b>RealSubject</b>

```java
@Slf4j
public class RealSubject implements Subject{
    @Override
    public String operation() {
        log.info("실제 객체 호출");
        sleep(1000);
        return "data";
    }

    private void sleep(int i) {
        try {
            Thread.sleep(i);
        }catch (InterruptedException e){
            e.printStackTrace();
        }
    }
}
```

<b>ProxyPatternClient</b>

```java
public class ProxyPatternClient {
    private Subject subject;

    public ProxyPatternClient(Subject subject) {
        this.subject = subject;
    }

    public void execute(){
        subject.operation();
    }
}
```

<b>ProxyPatternTest</b>

```java
public class ProxyPatternTest {

    @Test
    void noProxyTest(){
        RealSubject realSubject=new RealSubject();
        ProxyPatternClient client = new ProxyPatternClient(realSubject);
        client.execute();
        client.execute();
        client.execute();
    }
}
```

```text
RealSubject - 실제 객체 호출
RealSubject - 실제 객체 호출
RealSubject - 실제 객체 호출
```

이 데이터가 한번 조회하고 변하지 않는 데이터라면 어딘가에 저장하고 이미 조회한 데이터를 사용하는것이
성능상 좋다. 이런것을 캐시라고 한다.
프록시 패턴의 주용기능은 접근제어이다. 캐시도 접근 자체를 제어하는 기능 중 하나이다.

<br/>

### 프록시 패턴 적용후 

![화면 캡처 2022-06-25 114409](https://user-images.githubusercontent.com/37110261/175755277-fd243a07-afd4-4701-9448-850cef86605e.png)

<br/>

```java
@Slf4j
public class CacheProxy implements Subject{
    private Subject target;
    private String cacheValue;

    public CacheProxy(Subject target) {
        this.target = target;
    }

    @Override
    public String operation() {
        log.info("프록시 호출");
        if(cacheValue==null){
            cacheValue=target.operation();
        }
        return cacheValue;
    }
}
```

+ private Subject target: 클라이언트가 프록시를 호출하면 프록시가 최종적으로 실제 객체를 호출 해야한다. <br> 따라서 내부에 실제 객체의 참조를 가지고 있어야 한다. 이렇게 프록시가 호출하는 대상을 <b>target</b>이라고 한다.
+ operation(): cacheValue에 값이 없으면 실제 객체(target)를 호출해서 값을 구한다. <br> 그리고 구한 값을 cacheValue에 저장하고 반환한다. 만약 cacheValue에 값이 있으면 실제 객체를 전혀 호출하지 않고 캐시 값을 그대로 반환한다.<br> 따라서 처음 조회 이후에는 캐시에서 빠르게 데이터를 조회할 수 있다.

```java
public class ProxyPatternTest {

    @Test
    void noProxyTest(){
        RealSubject realSubject=new RealSubject();
        ProxyPatternClient client = new ProxyPatternClient(realSubject);
        client.execute();
        client.execute();
        client.execute();
    }

    @Test
    void cacheProxy(){
        Subject subject=new RealSubject();
        Subject cacheProxy=new CacheProxy(subject);
        ProxyPatternClient client = new ProxyPatternClient(cacheProxy);
        client.execute();
        client.execute();
        client.execute();
    }
}
```

<b>cacheProxyTest()</b>
<br/>

realSubject와 cacheProxy를 생성하고 둘을 연결한다. 결과적으로 cacheProxy가 realSubject를 참조하는 런타임 객체 의존 관계가 완성된다. <br> 그리고 client에 realSubject가 아닌 cacheProxy를 주입한다. 이 과정을 통해 client -> cacheProxy -> realSubject런타임 의존 관계가 완성된다.

<br/>

<b>실행 결과 </b>
```text
CacheProxy - 프록시 호출
RealSubject - 실제 객체 호출
CacheProxy - 프록시 호출
CacheProxy - 프록시 호출
```

<br/>

<b>결과 정리</b>
프록시 패턴의 핵심은 RealSubject코드와 클라이언트 코드를 <b>변경하지 않고</b> 프록시를 도입해서 접근 제어를 했다는 점이다.<br> 그리고 클라이언트 코드의 변경 없이 자유롭게 프록시를 넣고 뺄수 있다. 실제 클라이언트 입장에서는 프록시 객체가 주입되었는지, 실제 객체가 주입됬는지 알 수 없다.
