---
layout: post
title:  " [Java] Observer 패턴"
date:   2020-05-24
desc: "구독과 해지의 개념에서 보는 Observer패턴"
keywords: "Datastructure"
categories: [Datastructure]
tags: [datastructure]
icon: icon-html
---

Observer 패턴이란
------

Observer 패턴의 중요성
------

### 옵저버 패턴 구현 예시
#### 우리가 넷플릭스나 유튜브등에서 서비스를 신청하거나 해당 채널을 구독하게 되면 
#### 영상이나 영화가 추가 됬을때 구독자에게 알림이 간다. 그리고 우리는 전체목록을 
#### 조회할 수도 있으며 구독을 해지할 수도 있다. 이와같은 예시를 구현해보겠습니다.


### 인터페이스로 update함수를 구현하게 한다.
<br/>

``` java
public interface Observer {
    public abstract void update();
}
```
<br/>

### 구독의 대상이 되는 객체가 가져야할 공통함수를 추상클래스로 만든다.

<br/>

``` java
public abstract class Observable {
    public List<Observer> list=new ArrayList<>();
    public void subscribe(Observer observer){
        list.add(observer);
    }
    public void disposable(Observer observer){
        list.remove(observer);
    }
    public void notifyObservers(){
        for(Observer o:list){
            o.update();
        }
    }
}
```
<br/>
### 구독의 대상 구체 클래스 구현 -> 예시의 넷플릭스나 유튜브 채널이라 생각하자
### 영상이 추가될경우 구독자들에게 알림이 간다.

``` java
public class MovieStore extends Observable {
    private List<String> movies=new ArrayList<>();
    
    //새로운 영상이 추가되면 구독자들에게 통보
    public void add(String movie){
        movies.add(movie);
        notifyObservers();
    }
    
    public List<String> getMovieList(){
        return movies;
    }
}
```

<br/>
### 구독자들은 Observer인터페이스를 받아 update함수를 Override한다.

### 구독자 Hong
``` java
public class Hong implements Observer {
    private MovieStore movieStore;

    public Hong(MovieStore movieStore){
        this.movieStore=movieStore;
    }

    @Override
    public void update() {
        System.out.println("Hong님 : "+"새로운 영화가 무비스토어에 들어왔습니다.");
    }

    public List<String> getMovieList(){
        return movieStore.getMovieList();
    }
}

```
<br/>
### 구독자 Minsu
``` java
public class Minsu implements Observer{
    private MovieStore movieStore;

    public Minsu(MovieStore movieStore){
        this.movieStore=movieStore;
    }

    @Override
    public void update() {
        System.out.println("Minsu님 : "+"새로운 영화가 무비스토어에 들어왔습니다.");
    }

    public List<String> getMovieList(){
        for(String s:movieStore.getMovieList()){
            System.out.println(s);
        }
        return movieStore.getMovieList();
    }
}

```

### 메인클래스

``` java
public class Main {
    public static void main(String[] args){
        //영화를 판매하는 스토어
        MovieStore movieStore=new MovieStore();

        //새로운 영화가 들어올경우 알림을 받고싶은 사람들
        Hong hong=new Hong(movieStore);
        Minsu minsu=new Minsu(movieStore);

        //알림을 원하는 두 사람이 스토어에 구독 신청을 했다
        movieStore.subscribe(hong);
        movieStore.subscribe(minsu);

        movieStore.add("바람과 함께 사라지다");
        System.out.println("--------------------");
        movieStore.add("해리포터");
        System.out.println("--------------------");
        //민수가 알림을 받고 목록을 받아본다
        System.out.println("민수가 목록을 출력합니다");
        minsu.getMovieList();
    }
}

```

### 결과
``` java
Hong님 : 새로운 영화가 무비스토어에 들어왔습니다.
Minsu님 : 새로운 영화가 무비스토어에 들어왔습니다.
--------------------
Hong님 : 새로운 영화가 무비스토어에 들어왔습니다.
Minsu님 : 새로운 영화가 무비스토어에 들어왔습니다.
--------------------
민수가 목록을 출력합니다
바람과 함께 사라지다
해리포터

```
