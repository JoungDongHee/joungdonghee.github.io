---
tags:
  - 개발
  - 기술정리
created: 2025-05-20
type: 기술정리
aliases:
  - 자바 컬렉션 프레임워크
  - Java Collection Framework
---
# 📘 Collection Framework

컬렉션 프레임워크는 자바에서 데이터 그룹을 저장하고 관리하기 위한 클래스들을 **표준화**한 설계이다. 이렇게 표준화된 방식을 사용함으로써 다음과 같은 장점이 있다:

- 자료구조를 추상화하여 일관된 사용 가능
- 구현체를 유연하게 교체 가능 (예: `ArrayList` → `LinkedList`)
- 코드 재사용성 및 유지보수성 향상

![컬렉션 구조도 1](https://file-api.ksq9511.synology.me:5353/obsidian-files/image/20250520135843_image.png)
![컬렉션 구조도 2](https://file-api.ksq9511.synology.me:5353/obsidian-files/image/20250520135901_image.png)

컬렉션 프레임워크는 크게 아래 세 가지 주요 인터페이스로 구분된다:

- [[List]]
- [[Set]]
- [[Map]]

---

## List

- **특징**: 순서(인덱스)를 보장하며, **중복 허용**
- **대표 구현체**:
  - `ArrayList`: 배열 기반, 조회 속도 빠름
  - `LinkedList`: 연결 리스트 기반, 빈번한 삽입/삭제에 유리
  - `Vector`: 동기화 지원 (Legacy)

```java
List<String> list = new ArrayList<>();
list.add("apple");
list.add("banana");
list.add("apple"); // 중복 허용
```

---

## Set

- **특징**: 저장 순서를 보장하지 않거나(예: `HashSet`) 정의된 기준에 따라 정렬(예: `TreeSet`), **중복 허용 X**
- **대표 구현체**:
  - `HashSet`: 해시 기반, 가장 일반적
  - `LinkedHashSet`: 입력 순서를 유지
  - `TreeSet`: 정렬 상태 유지 (`Comparable` 또는 `Comparator` 필요)

```java
Set<String> set = new HashSet<>();
set.add("apple");
set.add("banana");
set.add("apple"); // 중복 무시
```

---

## Map

- **특징**: `key-value` 쌍으로 데이터를 저장. **key는 중복 불가**, **value는 중복 허용**
- **대표 구현체**:
  - `HashMap`: 일반적인 해시 기반 Map, 정렬 없음
  - `LinkedHashMap`: 입력 순서 유지
  - `TreeMap`: key 기준 정렬
  - `Hashtable`: 동기화 지원 (Legacy)

```java
Map<String, Integer> map = new HashMap<>();
map.put("apple", 3);
map.put("banana", 2);
map.put("apple", 5); // 기존 값 덮어씀
```

### 🔍 해싱(Hashing) 원리

`HashMap`, `HashSet`과 같은 컬렉션은 내부적으로 **해싱**을 통해 데이터를 저장하고 조회한다. 해싱은 다음과 같은 원리로 동작한다:

1. 객체의 `hashCode()` 값을 계산
2. 계산된 해시값을 기준으로 버킷(bucket) 배열의 인덱스를 결정
3. 해당 버킷에 데이터를 저장하거나 기존 값과 충돌 시 `equals()`를 비교하여 덮어쓰기 또는 연결 리스트/트리 구조로 처리

> 💡 좋은 해시 함수는 충돌을 최소화하여 `O(1)`에 가까운 조회 성능을 제공한다.

### 예시

```java
Map<String, String> map = new HashMap<>();
map.put("id", "user01");
map.get("id"); // key "id"의 hashCode()로 위치 계산 후 값 조회
```

---

## List vs Set vs Map

| 특성       | List           | Set            | Map                |
|------------|----------------|----------------|---------------------|
| 순서       | 유지됨         | 구현체에 따라 다름 | key 기준 없음        |
| 중복 허용  | 허용           | 허용하지 않음     | key: 불가, value: 허용 |
| 데이터 구조 | 단일 값        | 단일 값         | key-value 쌍         |

---

## 📚 관련 링크

- [Java 공식 Collection 문서](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Collection.html)
- [[Comparable vs Comparator]]
