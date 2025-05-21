---
tags:
  - 개발
  - 기술정리
  - 트랜잭션
  - 데이터베이스
aliases:
  - Commit vs Rollback
  - 커밋과 롤백 차이
  - 커밋 롤백 정리
created: 2025-05-16
type: 기술정리
---

# 📘 Rollback과 Commit 차이

트랜잭션을 제어할 때 핵심적으로 사용되는 명령어가 **Commit**과 **Rollback**이다. 이 둘은 [[트랜잭션(Transaction)]] 의 완료 여부에 따라 데이터베이스의 상태를 **확정**하거나 **되돌리는** 역할을 한다.

---

## 🔹 Commit

**Commit(커밋)** 은 트랜잭션에서 수행한 모든 변경 사항을 **영구적으로 반영**하는 명령이다.

- 트랜잭션의 **정상적인 종료**를 의미
- 변경 사항이 **디스크에 기록**되고 다른 트랜잭션에서도 조회 가능
- **복구 불가능** (Undo 불가)

예를 들어, 아래와 같이 계좌 이체 관련 `UPDATE` 쿼리를 실행했더라도, `COMMIT`을 하지 않으면 실제 DB에는 반영되지 않는다. 하지만 `COMMIT`이 실행되면 더 이상 롤백이 불가능하다.

📌 예시:
```sql
BEGIN;
UPDATE account SET balance = balance - 100 WHERE id = 1;
UPDATE account SET balance = balance + 100 WHERE id = 2;
COMMIT;
```

> ℹ️ 많은 [[RDBMS (관계형 데이터베이스 관리 시스템)]]에서는 `AUTO-COMMIT` 모드를 지원한다.  
> 이 기능은 명시적인 `COMMIT` 없이도 자동으로 트랜잭션을 종료해 편리하지만,  
> 반대로 실수로 인한 `DELETE`, `UPDATE`가 즉시 반영되어 **데이터 손실**을 일으킬 수 있다.  
> 따라서 중요한 업무에서는 **AUTO-COMMIT을 비활성화**하고 사용하는 것이 바람직하다.

---

## 🔹 Rollback

**Rollback(롤백)** 은 트랜잭션 수행 중 오류가 발생했을 때, **모든 변경을 취소**하고 이전 상태로 되돌리는 명령이다.

- 트랜잭션의 **비정상 종료** 처리
- 변경 사항이 **모두 무효화됨**
- 일부만 되돌리는 **부분 롤백**도 가능 (`SAVEPOINT` 사용 시)

📌 예시:
```sql
BEGIN;
UPDATE account SET balance = balance - 100 WHERE id = 1;
-- 오류 발생
ROLLBACK;
```

이 예시처럼 트랜잭션 도중 오류가 발생했을 때 `ROLLBACK`을 실행하면 이전 상태로 복구된다.

> ⚠️ 주의: `CREATE`, `DROP`, `ALTER`, `RENAME`, `TRUNCATE`와 같은 DDL(Data Definition Language) 명령어는 일반적으로 `ROLLBACK` 대상에 포함되지 않는다.  
> 예를 들어, `DELETE`문으로 데이터를 삭제한 경우 `AUTO-COMMIT`이 꺼져 있다면 `ROLLBACK`으로 복구가 가능하다.  
> 하지만 `DROP`이나 `TRUNCATE`는 복구가 불가능하므로, 실행 전에 주의가 필요하다.



---

## 🔁 차이 정리

| 항목         | Commit                         | Rollback                        |
|--------------|--------------------------------|----------------------------------|
| 목적         | 변경 사항 **확정**            | 변경 사항 **취소**             |
| 실행 시점    | 트랜잭션이 **성공적으로 종료**될 때 | 트랜잭션 **오류 발생 시**       |
| 결과         | 데이터가 영구 반영됨           | 모든 변경 사항이 무효화됨       |
| 복구 여부    | 복구 불가                      | 복구 가능                        |

---

## 📚 함께 참고하면 좋은 문서

- [[트랜잭션(Transaction)]]
- [[트랜잭션 격리 수준]]
- [[Savepoint]]
- [[MySQL]]
