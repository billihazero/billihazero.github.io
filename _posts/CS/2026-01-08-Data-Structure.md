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

#### LinkedList 기본

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

#### LinkedList 삽입, 삭제

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

```
    def popBack(self):
        if len(self) == 0 :
            return None

        #노드가 2개 이상인 경우
        prev = None
        tail = self.head

        # tail.next가 None이 될때까지
        while tail.next is not None:
            prev = tail
            tail = tail.next

        # tail = 마지막 노드, prev = 마지막 바로 앞 노드
        # tail.Next = None (tail 끝까지 온 것)
        if len(self) == 1 :
            self.head = None
        else:
            prev.next = None
            key = tail.key
            del tail
            self.size -= 1
            return key

```

#### LinkedList 탐색 + 제너레이터

한방향 연결리스트에서 노드를 탐색하는 방법과, 탐색 로직을 **제너레이터(generator)** 로 구현하는 메서드를 구현합니다.

```
def search(self,key):
    #key값의 node를 return, key값이 없다면 None return
    v = self.head
    while v is not None:
        if v.key == key:
            reuturn v
        else:
            v = v.next
    return None (or v = None)
```

제너레이터 함수는 반복 가능한 (iterable) 객체를 만들기 위해 사용하는 함수로, "for x in list" 와 같은 반복문에서 특히 유용합니다. 파이썬의 for문은 내부적으로 객체의 **이터레이터(iterator)**를 자동으로 요청하여 순차적으로 값을 가져옵니다. <br>

따라서 for x in SinglyLinkedList 와 같은 형태의 반복을 지원하려면, 파이썬이 해당 객체를 어떻게 순회해야 하는지 알 수 있도록 이터레이터를 제공해야합니다. 이를 위해 연결리스트를 구현할 때 특수 메서드인 **iterator**를 정의하는 것이 바람직 합니다.

```
def __iter__(self):
    v = self.head
    while v is not None:
        yield v
    v = v.next

```

yield는 함수의 실행을 완전히 종료하지 않고, 현재 상태를 유지한 채 값을 반환하는 키워드 이다. yield가 포함된 함수는 **제너레이터(generator)**가 되며, 반복 요청이 있을 때 마다 값을 하나씩 반환한다. <br>

제너레이터 함수에서 더이상 yield 할 것이 없고 함수의 실행이 끝나면, 파이썬은 내부적으로 StopIteration 예외를 발생시켜 반복이 종료된다.<br>

(순차적 자료구조에서 iterator 메서드를 구현해놓는 것이 좋다.)
