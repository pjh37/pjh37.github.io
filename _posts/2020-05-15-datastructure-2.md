---
layout: post
title:  "Queue에 대하여 "
date:   2020-05-16
desc: "c++ LinkedList 기반 구현, 원리"
keywords: "c++,dataStructure"
categories: [Datastructure]
tags: [DataStructure]
icon: icon-html
---

Queue 원리와 구현
===

---
## Queue 원리
### - 먼저들어간 데이터가 먼저나온다. (줄을 서 거나 등등, 일상 대부분이 Queue)

``` c++
class Queue{
private:
    struct Node{
        int data;
        Node* node;
    };
    Node* head;
    Node* rear;
    int count;
public:

    Queue():count(0),head(nullptr),rear(nullptr){}

    void push(int data){
        Node* node=new Node();
        node->data=data;
        if(isEmpty()){
            head=node;
        }else{
            rear->next=node;
        }
        rear=node;
        count++;
    }
    
    void pop(){
        if(isEmpty())return;
        Node* node=head;
        head=head->next;
        delete node;
        count--;
    }

    int front(){
        return head->data;
    }

    bool isEmpty(){
        return count==0;
    }
};
int main(){
    Queue* q=new Queue();
    q->push(1);
    q->push(2);
    q->push(3);
}

```
#### 결과

``` c++
1 2 3

```