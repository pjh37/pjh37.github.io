---
layout: post
title:  " [java] LinkedList 구현 "
date:   2020-11-04
desc: "java ,더블링크드리스트"
keywords: "c++,dataStructure,heap"
categories: [Datastructure]
tags: [DataStructure]
icon: icon-html
---

java 원리와 구현
====

<br/>


``` java

public class Node<T> {
    private T data;
    private Node<T> prev;
    private Node<T> next;

    public Node(){}

    public Node(T data){
        this.data=data;
        prev=null;
        next=null;
    }

    public Node<T> getPrev() {
        return prev;
    }

    public void setPrev(Node prev) {
        this.prev = prev;
    }

    public Node<T> getNext() {
        return next;
    }

    public void setNext(Node next) {
        this.next = next;
    }

    public T getData() {
        return data;
    }
}

```

<br/>

``` java
public class LinkedList<T> {
    private Node<T> head;
    private Node<T> tail;
    private int size;

    public LinkedList(){
        head=null;
        tail=null;
        size=0;
    }

    public Node getHead() {
        return head;
    }

    public void setHead(Node head) {
        this.head = head;
    }

    public Node getTail() {
        return tail;
    }

    public void setTail(Node tail) {
        this.tail = tail;
    }



    public void insertToHead(T data){
        Node<T> node=new Node<>(data);
        if(head==null){
            head=node;
            tail=node;
        }else if(head!=null){
            node.setNext(head);
            head.setPrev(node);
            head=node;
        }
        size++;
    }

    public void insertToTail(T data){
        if(size==0){
            insertToHead(data);
            return;
        }
        Node<T> node=new Node<>(data);
        node.setPrev(tail);
        tail.setNext(node);
        tail=node;
        size++;
    }

    public void insert(int index,T element){
        if(index==0){
            insertToHead(element);
        }else if(index==size-1){
            insertToTail(element);
        }else{
            Node<T> node=new Node<>(element);
            Node<T> prevNode=getNode(index-1);
            Node<T> nextNode=getNode(index);
            node.setPrev(prevNode);
            node.setNext(nextNode);
            prevNode.setNext(node);
            nextNode.setPrev(node);
        }
        size++;
    }

    public void printAll(){
        Node<T> pointer=head;
        while(pointer!=null){
            System.out.println(pointer.getData());
            pointer=pointer.getNext();
        }
    }

    public void removeFirst(){
        Node<T> removedNode=head;
        head=removedNode.getNext();
        head.setPrev(null);
        size--;
    }

    public void removeLast(){
        Node<T> removedNode=tail;
        tail=removedNode.getPrev();
        tail.setNext(null);
        size--;
    }

    public Node<T> getNode(int index){
        if(index<size/2){
            Node<T> node=head;
            for(int i=0;i<index;i++){
                node=node.getNext();
            }
            return node;
        }else{
            Node<T> node=tail;
            for(int i=size-1;i>index;i--){
                node=node.getPrev();
            }
            return node;
        }
    }

    public Integer getSize(){
        return size;
    }
}
```