---
layout: post
title:  " [java] RoundRobin 구현 "
date:   2020-11-12
desc: "RoundRobin 구현"
keywords: "java,dataStructure"
categories: [Datastructure]
tags: [DataStructure]
icon: icon-html
---

RoundRobin 구현
===


``` java
public class RoundRobin<T>{
    private final List<T> list;
    private final Iterator<T> iterator;
    private int index;

    public RoundRobin(List<T> list){
        this.list=list;
        this.index=0;
        this.iterator=new Iterator<>(){
            @Override
            public boolean hasNext() {
                return true;
            }

            @Override
            public T next(){
                T value=list.get(index);
                index=(index+1)%list.size();

                return value;
            }
        }
    }

    public T next() {
        return iterator.next();
    }

    public void add(T item) {
        list.add(item);
    }
}
```