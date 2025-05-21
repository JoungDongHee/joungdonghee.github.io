---
tags:
  - 개발
  - 기술정리
  - ArrayList
  - 자료구조
aliases:
  - java ArrayList
  - 자바 ArrayList
  - 자바 동적 배열
  - ArrayList
created: 2025-05-19
type: 기술정리
---

# 📘ArrayList

[[Collection Framework]] 중 하나인 `ArrayList`는 기존 배열 구조에 다양한 메서드를 추가한 **동적 배열 자료구조**입니다.

---

## 🔸 기존 배열의 한계

```java
int[] ints = new int[5];
ints[0] = 1;
ints[1] = 2;
ints[3] = 4;  // 잘못된 인덱스 사용 예시
ints[4] = 5;
ints[5] = 6;  // ArrayIndexOutOfBoundsException 발생
```

기존 배열은 다음과 같은 단점이 있습니다:

1. **크기 고정**  
   - 배열은 선언 시 고정된 크기를 가지며, 이후 동적으로 변경할 수 없습니다.
   - 부족한 경우 `ArrayIndexOutOfBoundsException`이 발생합니다.
   - 너무 크게 설정하면 메모리 낭비가 발생할 수 있습니다.

2. **삽입 및 삭제 비효율성**  
   - 배열 중간에 요소를 삽입/삭제할 경우 요소 이동이 필요해 성능 저하가 발생합니다.

> 💡 이러한 한계를 극복하기 위해 [[ArrayList 자료형]]가 도입되었습니다.

---

## 🔹 ArrayList의 특징

```java
// 크기 미지정 시
ArrayList<String> list = new ArrayList<>();

// 크기 지정 시
ArrayList<String> list5 = new ArrayList<>(5);
```

1. **동적 크기 조절**  
   - 초기 크기를 지정하지 않아도, 내부적으로 자동 확장됩니다.

2. **배열 기반 구현**  
   - 내부 배열을 사용하되, 크기 부족 시 자동으로 더 큰 배열로 복사합니다.

---

## ⚙️ 내부 동작: `add()`와 `grow()`

`add()` 메서드는 요소 추가 시 크기 부족 여부를 확인하고, 필요시 `grow()`를 호출해 배열을 확장합니다.

```java
private void add(E e, Object[] elementData, int s) {  
    if (s == elementData.length)  
        elementData = grow();  
    elementData[s] = e;  
    size = s + 1;  
}
```

배열 확장은 다음과 같은 방식으로 이루어집니다:

```java
private Object[] grow(int minCapacity) {  
    int oldCapacity = elementData.length;  
    if (oldCapacity > 0 || elementData != DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {  
        int newCapacity = ArraysSupport.newLength(
            oldCapacity, 
            minCapacity - oldCapacity, 
            oldCapacity >> 1  // 기존의 1.5배
        );  
        return elementData = Arrays.copyOf(elementData, newCapacity);  
    } else {  
        return elementData = new Object[Math.max(DEFAULT_CAPACITY, minCapacity)];  
    }  
}
```

> ℹ️ 배열 크기를 확장할 경우 **기존 배열의 약 1.5배로 증가**하며, `Arrays.copyOf()`를 통해 데이터를 복사합니다.

---

## 🧬 제네릭(Generic)의 사용

```java
public class ArrayList<E> extends AbstractList<E>  
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable {
    // ...
}
```

- `ArrayList<E>`는 **제네릭(Generic)**을 사용하여 타입 안정성을 보장합니다.
- 컴파일 시 타입 검사가 가능하여 타입 오류를 방지할 수 있습니다.

---

## 📉 ArrayList의 단점

1. **탐색 속도 저하**  
   - 특정 값을 찾기 위해 전체 순회가 필요하므로 시간 복잡도는 [[Big O 표기법#^bc3131|O(N)]]입니다.

2. **배열 복사 오버헤드**  
   - 배열이 가득 차면 `Arrays.copyOf()`로 복사하면서 성능 저하가 발생할 수 있습니다.

3. **중간 삽입/삭제의 비효율성**  
   - 중간 인덱스에 요소 추가/삭제 시, 이후 요소들을 이동해야 합니다.

---

## 📈 ArrayList의 장점

1. **빠른 임의 접근**  
   - `index`를 통한 접근은 [[Big O 표기법#^1244c4|O(1)]]로 매우 빠릅니다.

2. **입력 순서 보장**  
   - 입력된 순서가 유지되며, 중복 값도 허용됩니다.

3. **자동 크기 조정**  
   - 메모리 관리를 자동으로 수행합니다.

---

## 🧭 ArrayList 사용에 적합한 경우

- **크기가 자주 변경되지 않는 데이터**  
  - 예: 정해진 크기의 목록, 순차 처리되는 데이터

- **삽입/삭제 빈도가 낮은 경우**  
  - 삽입/삭제보다는 조회가 많은 케이스에 유리합니다.

> ✅ 데이터의 순서 유지가 중요하고, 임의 접근이 자주 필요한 경우 가장 적합한 자료구조입니다.
