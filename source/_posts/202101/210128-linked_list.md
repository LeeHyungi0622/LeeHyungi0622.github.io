---
title: 210128 자료구조(Data structure) Linked List(링크드 리스트)(작성중...)
date: 2021-01-28 16:30:42
tags:
  - Self-Development
  - Data-structure-Theory
  - Linked-list
  - Incomplete
categories:
  - Data-Structure-&-Algorithm
---

![](/images/post_images/210128_linked_list.png)

# **이번 포스팅에서 정리 할 내용은**

`링크드 리스트(Linked list)`에 대한 개념이다.

우선 링크드 리스트란 연결 리스트라고도 하며, 순차적으로 연결된 공간에 데이터를 나열하는 구조인 `배열과 대조적으로` 링크드 리스트는 떨어진 곳에 존재하는 데이터들을 화살표로 연결해서 관리하는 구조로 되어있다.

C언어에서는 주요한 데이터 구조이지만, Python에서는 리스트 타입이 링크드 리스트의 기능을 모두 지원한다.

## **링크드 리스트의 기본 구조와 용어**

- **노드(Node)** : 데이터 저장 단위(데이터값, 포인터)로 구성이 되어있다.
- **포인터(Pointer)** : 각 노드 안에서 다음이나 이전의 노드와의 연결 정보를 가지고 있다.

  <!-- more -->

## **링크드 리스트의 장단점(전통적인 C언어에서의 배열과 링크드 리스트)**

- **장점**
  - 배열은 미리 `미리 데이터 공간을 할당`해야되는 것에 반해 링크드 리스트는 미리 데이터 공간을 할당하지 않아도 된다.
- **단점**
  - 연결을 위한 별도의 데이터 공간이 필요하므로 저장공간 효율이 높지 않다.
  - 연결정보를 찾는 시간이 필요하기 때문에 접근 속도가 느리다.
  - 중간 데이터 삭제시, 앞 뒤 데이터의 연결을 재구성해야 하는 부가적인 작업이 필요하다.

## **Node 구현**

```python
class Node:
  def __init__(self, data, next=None):
    self.data = data
    self.next = next

# 포인터를 활용하여 Node와 Node를 서로 연결하기
node1 = Node(1)
node2 = Node(2)
node1.next = node2
head = node1
```

## **새로운 Node를 추가하는 method 작성하기**

```python
def add(data):
  node = head
  # node의 다음에 Node가 존재하면,
  while node.next:
    # node를 node의 다음 Node로 업데이트 해준다.
    node = node.nex
  # 제일 마지막 node에 추가하고자 하고자 하는 새로운 Node를 param 값으로 받은 data로 초기화 시켜서 다음 노드로 삽입해준다.
  node.next = Node(data)
```

## **생성된 링크드 리스트 출력해보기**

```python
node = head
while node.next:
  print(node.data)
  node = node.next
print(node.data)
```
