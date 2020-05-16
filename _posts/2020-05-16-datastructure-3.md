---
layout: post
title:  " Heap에 대하여 "
date:   2020-05-16
desc: "c++ ,우선순위 큐, 힙 구현"
keywords: "c++,dataStructure,heap"
categories: [Datastructure]
tags: [DataStructure]
icon: icon-html
---
Heap 원리와 구현
===

---
## Heap 이란
### - Heap은 완전 이진 트리의 일종으로 부모 노드와 자식노드간에 대소관계가 성립하는 자료구조이다
### - 부모노드 > 자식노드 일 경우 최대힙
### - 부모노드 < 자식노드 일 경우 최소힙 
### - 형제노드들간에는 관계가 없다
### - 최상단에 있는 노드는 가장 크거나 가장 작다 (우선 순위 큐를 구현 할때 사용하는 자료구조다)

<br/>

## Heap 구현

``` c++
class Heap {
private:
    int heap[10000];//배열의 크기는 알고리즘 문제등을 풀때 문제의 조건에 따라 정해주면 됩니다.
    int heap_size;
public:
    Heap() :heap_size(0) {
		memset(heap, 0, sizeof(heap));
    }

	void push(int data) {
        int target = heap_size+1;
        while (target != 1 && heap[target / 2] < data) {
            heap[target] = heap[target / 2];
            target /= 2;
        }
        heap[target] = data;
        heap_size++;
    }

    void pop() {
        int parent = 1, child = 2;
        int temp = heap[heap_size];
        while (true) {
            if (child + 1 < heap_size&&heap[child] < heap[child + 1]) {
                child++;
			}
            if (temp >= heap[child])break;
            heap[parent] = heap[child];
            parent = child;
            child *= 2;
		}
        heap[parent] = temp;
        heap_size--;
	}

    int top() {
        return heap[1];
	}
    bool isEmpty() {
        return heap_size == 0;
    }
};
int main(){
    ios_base::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
	
    Heap* hp = new Heap();
    hp->push(3);
    hp->push(1);
    hp->push(4);
    hp->push(2);
	hp->push(5);
    while (!hp->isEmpty()) {
        cout << hp->top() << " "; hp->pop();
    }
	
    return 0;
}
```
#### 결과

``` c++
5 4 3 2 1

```