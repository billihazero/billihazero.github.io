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

![linkedlist](/post_images/list_img.png)

### ArrayList와 LinkedList의 차이점

Array는 메모리 상에서 연속되 공간을 차지하기 때문에, 각 요소는 인덱스를 통해 직접 접근할 수 있습니다. <br>

배열의 시작 주소를 알고 있다면 i 번째 인덱스의 요소를 읽거나 수정하는데 O(1)의 시간복잡도를 가집니다.<br>

조회 성능이 매우 빠른 것이 장점이라면, 중간에 요소를 삽입하거나 삭제를 하기 위해선 뒤에 있는 요소들을 모두 이동(O(n))시켜야 하므로, 이 경우에는 성능이 저하될 수 있습니다.

LinkedList는 Node라는 단위로 구성되며, 각 Node는 데이터 값과 다음 노드를 가리키는 주소를 함께 저장합니다.<br>

이 구조는 메모리 상에서 연속성을 유지하지 않으며, 특정 인덱스의 요소로 접근하기 위해서는 Head Node부터 시작하여 순차적으로 다음 노드를 따라가야 합니다. <br>

두 번째 요소에 접근하려면 Head Node 부터 시작하여 두 번 이동해야 하므로, O(n)의 시간복잡도를 가집니다. <br>

노드 간의 연결만 변경하면 되기 때문에 중간 삽입이나 삭제는 O(1)에 가깝게 처리할 수 있습니다. <br>

LinkedList에는 한방향 연결리스트, 양방향 연결리스트가 있습니다.

---

한방향 연결리스트(Singly Linked List)는 Node 객체들이 연결된 구조로 이루어져 있습니다.

Node 클래스를 구현해 봅니다.

### Singly Linked List 기본

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

#### Singly Linked List 삽입, 삭제

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

#### Singly Linked List 탐색 + 제너레이터

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

yield는 함수의 실행을 완전히 종료하지 않고, 현재 상태를 유지한 채 값을 반환하는 키워드 입니다. yield가 포함된 함수는 **제너레이터(generator)**가 되며, 반복 요청이 있을 때 마다 값을 하나씩 반환합니다. <br>

제너레이터 함수에서 더이상 yield 할 것이 없고 함수의 실행이 끝나면, 파이썬은 내부적으로 StopIteration 예외를 발생시켜 반복이 종료됩니다.<br>

(순차적 자료구조에서 iterator 메서드를 구현해놓는 것이 좋습니다.)

### Doubly Linked List 기본

한방향 연결리스트(Singly Linked List)는 각 노드가 다음 노드만을 가리키는 구조입니다. 이 구조에서는 **tail 노드의 이전 노드(prev)를 알기 위해 처음부터 순회해야 하므로 O(n)의 시간이 소요되는 단점**이 있습니다.

이러한 단점을 보완하기 위해 등장한 자료구조가 **양방향 연결리스트(Double Linked List)** 입니다. 양방향 연결 리스트는 각 노드가 이전 노드(prev)와 다음 노드(next)를 모두 가리키도록 구성되어 있어, 앞뒤 방향으로의 이동이 가능하며 특정 노드의 삭제나 삽입을 더 효율적으로 처리할 수 있습니다.

다만, 노드마다 관리해야 할 링크가 하나 더 늘어나기 때문에 메모리 사용량이 증가하고 구현 복잡도가 높아질 수 있다는 단점도 존재합니다.

#### 원형 양방향 연결 리스트 (Circularly Doubly Linked List)

양방향 연결리스트를 확장한 형태로 **원형 양방향 연결 리스트 (Circularly Doubly Linked List)** 가 있습니다.

이 구조에서는

- tail 노드의 next가 head 노드를 가리키고
- head 노드의 prev가 tail 노드를 가리키는 방식

으로 연결되어, 리스트의 시작과 끝이 하나의 원으로 이어집니다.

이 구조를 구현할 때는 보통 **빈 리스트를 하나의 노드로 표현**하는데, 이를 dummy node라고 부릅니다.

dummy node는 실제 데이터를 저장하지 않으며, 리스트에서 **어디가 시작인지 표시해주는 기준(마커)역할**을 합니다.

```
class Node:
    def __init__(self, key=None):
        self.key = key
        self.next = self
        self.prev = self

class DoublyLinkedList:
    def __init__(self):
        self.head = Node()
        self.size = 0
    def __iter ...
    def __str ...
    def __len ...

```

#### 양방향 연결리스트에서 Splice 연산 적용

splice(a, b, x): 현재 리스트에서 연속 구간 [a,,,b] (a부터 b까지)를 잘라낸 다음, 노드 x 에 그 구간을 그대로 붙입니다.

##### Splice 연산의 조건

Splice 연산이 정상적으로 동작하기 위해서는 다음 조건들이 반드시 만족되어야 합니다.

- 조건 1: a -> ... -> b 관계가 성립해야합니다.
- 조건 2: a와 b 사이에 head 노드가 존재하면 안됩니다.

![Circularly Doubly Linked List](/post_images/circlyList.png)

```
def __splice__(self, a, b, x):

    # [a..b] 구간을 떼어내기
    a_prev = a.prev
    b_next = b.next
    a_prev.next = b_next
    b_next.prev = a_prev

    # [a..b] 구간을 x 뒤에 삽입하기
    x_next = x.next

    x.next = a
    a.prev = x

    b.next = x_next
    x_next.prev = b
```

#### Splice 연산을 활용한 삽입, 이동, 탐색, 삭제 연산

##### 이동연산

이동연산에는 총 4개의 함수가 있습니다.
moveAfter/Before, insertAfter/Before

```
def moveAfter(self, a, x): #노드a를 노드x 다음으로 이동
    => splice(a, a, x) # a ~ a 까지 x의 뒤로 이동

def moveBefore(self, a, x): #노드 a를 노드 x 전으로 이동
    splice(a, a, x.prev) # a~a 까지 x 전으로 이동

def insertAfter(self, x, key): #노드 x 다음에 노드(key) = v를 삽입
    => moveAfter(v, x) => splice(v, v, x)

def insertBefore(self, x, key): #노드 x 전에 노드(key) = v 를 삽입
    => moveBefore(v, x) => aplice(v, v, x.prev)
```

##### 탐색연산

```
def search(self, key):
    v = self.head #dummy
    while.next !== self.head: #마지막이 아닐경우
        if v.key == key:
            return v
        v = v.next
    return None # 못 찾았을 때

```

##### 삭제연산

```
def remove(x): #노드 x 를 삭제
    if x is None or x == self.head # x가 없거나, head를 지우면 안됨
        return
    x.prev.next = x.next
    x.next.prev = x.prev
```

## 해시 테이블 (Hash Table)

해시 테이블(Hash Table)은 **삽입, 삭제, 탐색** 연산을 **평균적으로 O(1)**이라는 매우 빠른 시간 복잡도로 수행할 수 있는 자료구조 입니다.

파이썬에서는 해시 테이블이 **dictionary(dict)** 현태로 구현되어 있습니다.

### 파이썬 Dictionary 구조

dictionary는 **key-value 쌍** 으로 데이터를 저장합니다.

```
D = {
    2017276: "홍길동",
    2018209: "김철수",
    2018229: "톰 크루즈"
}
```

key: 학번 - value: 이름 형태로 선언되었습니다.

여기서 각 key는 해시 함수(hash function)을 통해 특정 인덱스로 변환되어 테이블에 저장됩니다.

### 해시 함수와 인덱싱

예를 들어 해시함수가 다음과 같다고 가정해봅니다.

```
f(k) = k % 10
```

이 경우, 해시 테이블의 인덱스는 0~9가 됩니다.

```
key = 2017276 value = 홍길동 f(k) = 6
key = 2018209 value = 김철수 f(k) = 9
key = 2018229 value = 톰 크루즈 f(k) = 9
```

이 때 김철수와 톰크루즈는 동일한 인덱스인 9에 매핑됩니다.

### Collision

이미 9번 인덱스에 김철수가 저장되어 있는데, 같은 위치에 톰 크루즈를 저장하려고 하면 문제가 발생합니다.

이처럼 서로 다른 **key가 동일한 해시 값(인덱스)을 가지는 현상을 Collision(충돌)** 이라고 합니다.

충돌은 해시테이블에서 피할 수 없는 문제이며, 이를 해결하기 위한 전략을 **Collision Resolution Method** 라고 합니다.

### 해시 테이블의 구성 요소

1. Table(보통 배열 or 리스트)
2. Hash Funciton
3. Collision Resolution Method

### 좋은 해시 함수란 ?

해시 함수는 다음과 같은 성질을 갖는 것이 이상적입니다.

- 충돌이 적을 것
- Key들이 테이블 전체에 **고르게 분포** 될 것

이론적으로 **충돌이 절대 발생하지 않는 해시 함수, Perfect Hash Function**이 가장 이상적입니다.

그러나, PHF는

- 모든 key 집합을 미리 알고 있어야 하고,
- 동적 데이터에는 적용하기 어렵기 때문에

현실적으로 사용하기란 어렵습니다.

### Universal Hash Funtion

현실적인 대안으로 **Universal Hash Function**이 사용됩니다.

서로 다른 두 key x, y에 대해

```
Pr(f(x) == f(y)) = 1 / m (m = 테이블의 크기)
```

이 성질을 만족하면 universal hash function 이라고 합니다.

만약 충돌 확률이

```
Pr(f(x) == f(y)) ≤ c / m   (c > 0)
```

라면 이를 c-universal hash function 이라고 합니다.

### 대표적인 해시 함수 기법

1. Division method
2. Multiplication method
3. Folding
4. Mid-square method

### 충돌 회피 방법

앞서 살펴본 것 처럼 Perfect Hash Function(PHF)이 아닌 이상, 해시 테이블에서는 **충돌(Collision)** 이 반드시 발생합니다.

이미 어떤 slot에 다른 key가 저장되어 있을 때,

**이 데이터를 어디에 저장할 것인가?**

이 문제를 해결하는 방법을 **충돌 회피 방법(Collision Resolution Method)** 이라고 합니다.

### Open Addressing 기법

충돌이 발생했을 때, **테이블 내부에서 다른 빈 공간을 찾아 저장하는 방식**을 **Open Addressing** 이라고 합니다.

Open Addressing에는 대표적으로 다음과 같은 기법들이 있습니다.

- Linear Probing
- Quadratic Probing
- Double Hashing

#### Linear Probing

Linear Probing은 충돌이 발생하면 **현재 위치에서 아래 방향(다음 인덱스)으로 한 칸씩 이동** 하며 비어 있는 slot을 찾는 방식입니다.

![linear probing](/post_images/linear_probing.png)
