---
tags:
  - 개발
  - 기술정리
  - LinkedList
  - 자료구조
aliases:
  - java LinkedList
  - 자바 LinkedList
  - 자바 연결 리스트
  - LinkedList
created: 2025-05-19
type: 기술정리
---

# 📘 LinkedList

[[ArrayList 자료형]]와 동일하게 [[Collection Framework]]에 속한 `LinkedList`는 이름처럼 각 노드가 서로의 **주소를 참조(link)** 하여 연결된 **연결 리스트 자료구조**입니다.


![image.png](https://file-api.ksq9511.synology.me:5353/obsidian-files/image/20250519155055_image.png)


---

## 🔹 구조 이해

`LinkedList`는 내부적으로 `Node` 클래스를 사용하여 구성됩니다.

```java
private static class Node<E> {  
    E item;  
    Node<E> next;  
    Node<E> prev;  
  
    Node(Node<E> prev, E element, Node<E> next) {  
        this.item = element;  
        this.next = next;  
        this.prev = prev;  
    }  
}
```

- 각 `Node`는 자신 앞의 노드(`prev`)와 뒤의 노드(`next`)를 참조합니다.
- 가리키는 노드가 없을 경우 `null`입니다.

---

## ➕ 데이터 추가

### ✅ LinkedList의 특징

- `LinkedList`는 [[ArrayList 자료형]]와 다르게 **중간 삽입/삭제 시 복사가 필요 없습니다**.
- 포인터(`prev`, `next`)의 연결만 변경하면 되므로 성능상 유리합니다.

### ✅ 첫 번째 위치에 데이터 추가

다음은 `LinkedList`에서 첫 번째 위치에 데이터를 추가하는 내부 메서드입니다.

```java
private void linkFirst(E e) {  
    final Node<E> f = first;
    final Node<E> newNode = new Node<>(null, e, f);
    first = newNode;
    if (f == null) {
        last = newNode;
    } else {
        f.prev = newNode;
    }
    size++;
    modCount++;
}
```

**작동 순서:**
1. 현재 첫 번째 노드(`first`)를 변수 `f`로 저장합니다.
2. `prev`가 `null`이고, `next`가 기존 첫 번째 노드를 참조하는 새 노드를 생성합니다.
3. `first` 포인터를 새 노드로 갱신합니다.
4. 만약 리스트가 비어 있었다면 `last` 포인터도 새 노드로 설정합니다.
5. 기존 첫 번째 노드가 존재하면 그 노드의 `prev`를 새 노드로 연결합니다.

> ✅ 연결 포인터만 갱신되므로 **시간 복잡도는 O(1)** 입니다.

### ✅ 마지막 위치에 데이터 추가

다음은 `LinkedList`에서 마지막 위치에 데이터를 추가하는 내부 메서드입니다.

```java
private void linkLast(E e) {
    final Node<E> l = last;
    final Node<E> newNode = new Node<>(l, e, null);
    last = newNode;
    if (l == null) {
        first = newNode;
    } else {
        l.next = newNode;
    }
    size++;
    modCount++;
}
```

**작동 순서:**
1. 현재 마지막 노드(`last`)를 변수 `l`로 저장합니다.
2. `prev`가 기존 마지막 노드를 가리키고, `next`가 `null`인 새 노드를 생성합니다.
3. `last` 포인터를 새 노드로 갱신합니다.
4. 리스트가 비어 있었다면 `first` 포인터도 새 노드로 설정합니다.
5. 기존 마지막 노드가 존재하면 그 노드의 `next`를 새 노드로 연결합니다.

> ✅ 연결 포인터만 갱신되므로 **시간 복잡도는 O(1)**입니다.


---

## 🔄 중간 데이터 삽입
### 🧩 중간 위치에 데이터 삽입

`LinkedList`는 인덱스를 기준으로 중간에 데이터를 삽입할 수도 있습니다. 이때 다음 로직이 수행됩니다.

```java
public void add(int index, E element) {  
    checkPositionIndex(index);  
  
    if (index == size)  
        linkLast(element);  
    else  
        linkBefore(element, node(index));  
}
```

- `index == size`: 맨 마지막 위치에 추가 → `linkLast()` 호출
- 그 외의 경우 → `node(index)`로 해당 위치의 노드를 찾고, `linkBefore()`로 앞에 삽입

노드를 탐색하는 `node()` 메서드는 아래와 같이 동작합니다:

```java
Node<E> node(int index) {
    if (index < (size >> 1)) {
        Node<E> x = first;
        for (int i = 0; i < index; i++)
            x = x.next;
        return x;
    } else {
        Node<E> x = last;
        for (int i = size - 1; i > index; i--)
            x = x.prev;
        return x;
    }
}
```

- 인덱스가 앞쪽에 가까우면 `first`부터 탐색
- 뒤쪽이면 `last`부터 역순 탐색 → 효율성 고려

> [!info] 중간 삽입 시 주의
> 순차 탐색을 거쳐야 하므로 **시간 복잡도는 O(N)**이며, 많은 삽입이 필요한 경우 구조 선택에 주의해야 합니다.

---

## ❌ 데이터 삭제

- **첫 번째, 마지막 데이터 삭제**는 포인터만 변경하면 되므로 **O(1)** 입니다.
- **중간 데이터 삭제**는 탐색이 필요하므로 **O(N)** 입니다.

> 📌 삭제 역시 삽입과 동일하게 연결만 해제하면 되므로, 구조적으로는 효율적입니다.

---

## 🧭 LinkedList가 적합한 경우

- 데이터의 **삽입/삭제가 빈번한 경우**
- 데이터의 위치 접근보다 **데이터 흐름(연결)** 이 중요한 경우

> ✅ 반면, 임의 위치 접근이 자주 필요한 경우에는 [[ArrayList 자료형]]가 더 적합합니다.

---
