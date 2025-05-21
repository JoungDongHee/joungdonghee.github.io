---
tags:
  - "#Redis명령어"
  - "#Redis자료형"
  - "#인메모리데이터베이스"
  - Redis숫자형
  - SET명령어
created: 2025-05-12
type: 기술정리
aliases:
  - Redis 명령어
  - Redis 사용법
  - Redis 튜토리얼
  - Redis Cheatsheet
  - Redis Examples
  - Redis 숫자형 명령어
  - Redis 정수/실수 명령어
---

# 📘 Redis 명령어 정리

Redis는 [[In-Memory Database (IMDB)|메모리 기반 데이터베이스]]로, 다양한 자료형에 따라 수많은 명령어를 제공합니다.  
때문에 [Redis 공식문서](https://redis.io/docs/latest/commands/)에서 명령어를 확인하며 사용하는 것이 좋습니다.

> 📎 관련 문서: [[Redis 핵심 정리]]

---

## 🔍 명령어 탐색 방법

1. Redis 공식 문서의 "Filter By Group"에서 사용하려는 자료형(Data Type)을 선택합니다.  
   ![image.png](https://file-api.ksq9511.synology.me:5353/obsidian-files/image/20250512141958_image.png)

2. 예를 들어 `String` 자료형을 선택하면 다음과 같은 명령어들을 확인할 수 있습니다.  
   ![image.png](https://file-api.ksq9511.synology.me:5353/obsidian-files/image/20250512142165_image.png)

3. 명령어 문서의 표기 방식은 다음과 같은 규칙을 따릅니다:  
   ![image.png](https://file-api.ksq9511.synology.me:5353/obsidian-files/image/20250512142230_image.png)

   - `SET key value`: 필수 인자
   - `[ ]`: 선택 옵션
   - `|`: OR 연산자 (둘 중 하나만 사용 가능)

---

## 📌 주요 명령어 및 옵션 예시

### 🔹 SET

값을 저장하는 기본 명령어입니다.

```shell
SET color red
```

**응답**:
```shell
OK
```

---

#### 🧩 옵션: XX

**설명**: Key가 이미 존재할 때만 값을 설정합니다.

```shell
SET color "hi there" XX
```

**응답**:
```shell
OK
```

> Key가 존재하지 않으면 설정되지 않고 `nil`을 반환합니다.

```shell
(nil)
```

---

#### 🧩 옵션: NX

**설명**: Key가 존재하지 않을 때만 값을 설정합니다.

```shell
SET color "blue" NX
```

**응답**:
```shell
OK
```

> Key가 이미 존재할 경우:
```shell
(nil)
```

---

#### 🧩 옵션: EX

**설명**: 초 단위 만료 시간을 설정합니다.

```shell
SET tempKey "hello" EX 2
```

**응답**:
```shell
OK
```

> ⏳ 2초 후:
```shell
GET tempKey
(nil)
```

---

### 🔹 MSET

복수 개의 Key-Value 쌍을 한 번에 설정합니다.

```shell
MSET color red model toyota
```

**응답**:
```shell
OK
```

---

### 🔹 GET

Key의 값을 조회합니다.

```shell
GET color
```

**응답**:
```shell
"red"
```

---



### 🔹 MGET

여러 Key의 값을 배열 형태로 반환합니다.

```shell
MGET color model
```

**응답**:
```shell
1) "red"
2) "toyota"
```

---

### 🔹 DEL

Key를 삭제합니다. 삭제된 Key의 수를 반환합니다.

```shell
DEL color
```

**응답**:
```shell
(integer) 1
```

> 존재하지 않는 Key를 삭제할 경우:
```shell
(integer) 0
```

---

### 🔹 GETRANGE

문자열의 일부를 잘라서 반환합니다.  
예: `"toyota"` 저장 시

```shell
GETRANGE model 0 2
```

**응답**:
```shell
"toy"
```

---

### 🔹 SETRANGE

문자열의 일부를 덮어쓰기 합니다.

```shell
SETRANGE model 2 blue
```

**응답**:
```shell
(integer) 6
```

```shell
GET model
"toblue"
```

## Number 명령어

Redis는 값을 문자열로 저장하지만, **정수 또는 부동소수점 형태의 문자열**에 대해서는 숫자 연산 명령어를 지원합니다.  
Redis는 이를 내부적으로 자동 해석하여 연산합니다.

> ⚠️ `"hello"` 같은 숫자가 아닌 문자열에 연산 명령어(`INCR`, `DECR` 등)를 사용하면 에러가 발생합니다.

```shell
SET foo hello
INCR foo
# => (error) ERR value is not an integer or out of range
```

반면, 숫자형 문자열은 정상적으로 처리됩니다.

```shell
SET count 5
INCR count
# => (integer) 6
```

---

### INCR

지정한 Key의 값을 1 증가시킵니다.

```shell
SET age 20
INCR age
```

**응답**:
```shell
(integer) 21
```

> `INCR`은 Redis 내부에서 **원자적으로 처리**되므로, `GET → +1 → SET`처럼 여러 명령어를 사용하는 것보다 동시성 측면에서 안전하고 효율적입니다.

---

### DECR

지정한 Key의 값을 1 감소시킵니다.

```shell
DECR age
```

---

### INCRBY

지정한 값만큼 증가시킵니다.

```shell
INCRBY age 40
```

---

### DECRBY

지정한 값만큼 감소시킵니다.

```shell
DECRBY age 40
```

---

### INCRBYFLOAT

지정한 소수점 수만큼 증가 또는 감소시킵니다.  
음수를 넣으면 감소합니다.

```shell
INCRBYFLOAT age -3.333
```

**응답**:
```shell
"17.667"
```

---
## ✅ 참고하면 좋은 키워드

- [[Redis 자료형]]
- [[Redis 만료 및 지속성]]
- [[Redis 성능 최적화 기법]]
