---
title: 자료구조 이론 정리
#date: 2013-12-24 23:30:09
categories:
  - CS

tags:
  - Data Structure
  - 신찬수 교수님 유튜브

photos:
---

신찬수 교수님의 유튜브 자료구조 강의를 시청하며 강의 내용을 정리한 글입니다. 강의를 시청하면서 이해한 개념들을 하나씩 정리하고, 이후에도 계속해서 내용을 추가해 나갈 예정입니다.

[신찬수 교수님 유튜브](https://www.youtube.com/watch?v=PIidtIBCjEg&list=PLsMufJgu5933ZkBCHS7bQTx0bncjwi4PK)

## Queue

> queue는 먼저 저장한 데이터가 먼저 출력되는 선입선출 FIFO 형식으로 데이터를 저장하는 자료구조입니다. <br>
> queue의 뒤에서 데이터를 추가하는 것을 enqueue, 앞에서 데이터를 꺼내는 것을 dequeue라고 합니다.(stack에서 enqueue = push, dequeue =pop)

```
Index:  0   1   2   3   4
        ┌───┬───┬───┬───┬───┐
Array:  │(5)│ 8 │ 3 │ 7 │ 2 │
        └───┴───┴───┴───┴───┘

enqueue(5)
enqueue(8)
enqueue(3)
enqueue(7)
enqueue(2)

dequeue(5)
```

---

Queue를 클래스로 구현해봅니다.

```
class Queue:
    def __init__(self):
        self.items = [] #빈 리스트
        self.front_index = 0
    def enqueue(self, val):
        self.items.append(val)
    def dequeue(self):
        if(self.front_index == len(self.items)):
            print("Queue is Empty")
        else:
            X = self.items[self.front_index]
            self.front_index += 1
            return X
```

Queue 클래스를 구현한 뒤 이를 이용하여 요세푸스 문제를 풀어보았습니다.

```
n, k = map(int, input().split())

class Queue:
    (위의 구현 내용...)
    def size(self):
        return len(self.items) - self.front_index

def joseph(n, k):
    q = Queue() # 사람들을 list에 삽입

    for i in range (1, n+1):
        q.enqueue(i)

    while q.size() > 1: # 한명 남을 때 까지 반복
        for _ in range(k-1):     # k-1번까지
            q.enqueue(q.dequeue())

        removed = q.dequeue() #k번째는 제거
        print("제거된 사람: ", removed)
    return q.dequeue()

result = joseph(n, k)
print("마지막 생존자: " , result)
```
