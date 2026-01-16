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

## Linked List (연결 리스트)

> Linked List는 Node(key, link)라는 구조체가 연결되는 형식으로 데이터를 저장하는 자료구조 입니다.

![alt text](/post_images/list_img.png)

### ArrayList와 LinkedList의 차이점

#### ArrayList

Array는 메모리 상에서 연속되 공간을 차지하기 때문에, 각 요소는 인덱스를 통해 직접 접근할 수 있습니다. <br>

배열의 시작 주소를 알고 있다면 i 번째 인덱스의 요소를 읽거나 수정하는데 O(1)의 시간복잡도를 가집니다.<br>

조회 성능이 매우 빠른 것이 장점이라면, 중간에 요소를 삽입하거나 삭제를 하기 위해선 뒤에 있는 요소들을 모두 이동(O(n))시켜야 하므로, 이 경우에는 성능이 저하될 수 있습니다.

#### LinkedList

LinkedList는 Node라는 단위로 구성되며, 각 Node는 데이터 값과 다음 노드를 가리키는 주소를 함께 저장합니다.<br>

이 구조는 메모리 상에서 연속성을 유지하지 않으며, 특정 인덱스의 요소로 접근하기 위해서는 Head Node부터 시작하여 순차적으로 다음 노드를 따라가야 합니다. <br>

두 번째 요소에 접근하려면 Head Node 부터 시작하여 두 번 이동해야 하므로, O(n)의 시간복잡도를 가집니다. <br>

노드 간의 연결만 변경하면 되기 때문에 중간 삽입이나 삭제는 O(1)에 가깝게 처리할 수 있습니다. <br>

LinkedList에는 한방향 연결리스트, 양방향 연결리스트가 있습니다.

---

한방향 연결리스트(Singly Linked List)는 Node 객체들이 연결된 구조로 이루어져 있습니다.

Node 클래스를 구현해 봅니다.

```
class Node:
    def __init__(self, key=None):
        self.key = key (값 저장)
        self.next = None (주소 저장)
    def __str__(self):
        return str(self.key)

a = Node(3)
b = Node(9)
c = Node(-1)
d = Node(2)
```

각 Node는 각각의 변수에 할당되어 있지만, 실제로 연결리스트를 순회하는 데에는 모든 노드의 변수를 알고 있을 필요가 없습니다. <br>

첫 번째 노드인 Head Node만 알고 있으면, next 참조를 따라가면 나머지 모든 노드에 접근 할 수 있습니다.

SinglyLinkedList 클래스를 구현합니다. 기본 구성요소를 정의하고, 노드의 삽입과 삭제를 처리하는 메서드를 구현합니다.

```
Class SinglyLinkedList:
    def __init__(self):
        self.head =None  #Head Node의 주소
        self.size = 0    #LinkedList를 구성하는 Node의 개수
    def__len__(self):
        return self.size
```

```
    def pushFront(self, key):
        new_node = Node(key)
        new_node.next = self.head
        self_head = new_node
        self.size +=1

    def pushBack(self, key):
        if len(self) == 0:
            return None
        else:
            new_node = Node(key)
            tail = self.head
            while(tail.next !== None):
                tail.next = tail
            tail.next = new_node
            self.size -= 1
```

```
    def popFront(self, key):
        if self.head is None:
            return None
        else:
            removed = self.head
            self.head = self.head.next
            self.size -= 1
            return removed.key

```
