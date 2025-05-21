---
date: 2024-11-27 00:11
author: Joung Dong Hee
tags: [MySQL, ConnectionError, TooManyConnections, DB관리]
aliases: [MySQL 최대 커넥션 오류, Too Many Connections 에러, MySQL 커넥션 수 초과]
---

# MySQL Too Many Connections 에러

MySQL 서버에서 [[DB Connection]] 수가 설정된 **최대 커넥션 수 (`max_connections`)** 를 초과하면 `Too many connections` 에러가 발생합니다.

기본적으로 MySQL은 별도의 설정이 없는 경우 **최대 151개 커넥션**만 허용합니다. 이 숫자를 초과하면 새로운 커넥션을 생성할 수 없어 데이터 조회 등 작업이 불가능해집니다.

---

## 최대 커넥션 수 확인

아래 쿼리를 통해 현재 설정된 최대 커넥션 수를 확인할 수 있습니다:

```sql
SHOW VARIABLES LIKE '%max_connect%';
```

| Variable_name         | Value |
|-----------------------|-------|
| extra_max_connections | 1     |
| max_connect_errors    | 100   |
| max_connections       | 151   |

---

## 커넥션 상태 확인

현재 DB의 커넥션 상태를 확인하려면 다음 쿼리를 사용합니다:

```sql
SHOW STATUS LIKE '%connect%';
```

| Variable_name                                 | Value               |
|-----------------------------------------------|---------------------|
| Aborted_connects                              | 200                 |
| Aborted_connects_preauth                      | 21                  |
| Connection_errors_accept                      | 0                   |
| Connection_errors_internal                    | 179                 |
| Connection_errors_max_connections             | 0                   |
| Connections                                   | 51663               |
| Max_used_connections                          | 152                 |
| Max_used_connections_time                     | 2024-11-25 13:30:19 |
| Threads_connected                             | 121                 |

이 중 `Max_used_connections` 는 과거에 동시에 사용된 최대 커넥션 수를 의미하며, `Threads_connected` 는 현재 활성화된 커넥션 수를 나타냅니다.

---

## 관련 문서

- [[DB Connection]]
- [[MySQL 성능 모니터링]]
