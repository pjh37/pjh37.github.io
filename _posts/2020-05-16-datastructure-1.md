---
layout: post
title:  "Stack에 대하여 "
date:   2020-05-16
desc: "c++ LinkedList 기반 구현, 원리"
keywords: "c++,dataStructure"
categories: [Datastructure]
tags: [DataStructure]
icon: icon-html
---

Stack 원리와 구현
===

---
## Stack 원리
### - 먼저들어간 데이터가 나중에 나온다 (재귀 함수의 원리와 같다)

``` c++
class Stack{
private:
    struct Node{
        int data;
        Node* next;
    };
    Node* top;
    int count;
public:
    Stack():count(0),top(nullptr){}

    void push(int data){
        Node* node=new Node();
        node->data=data;
        node->next=top;
        top=node;
        count++;
    }

    void pop(){
        if(isEmpty())return;
        Node* node=top;
        top=node->next;
        delete node;
        count--;
    }

    int mTop(){
        return top->data;
    }

    bool isEmpty(){
        return count==0;
    }
}
int main(){
    Stack* st=new Stack();
    st->push(1);
    st->push(2);
    st->push(3);
}

```
#### 결과

``` c++
3 2 1

```