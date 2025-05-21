---
tags:
  - 데이터베이스
  - 기술정리
  - RDBMS
  - SQL
  - "#관계형데이터모델"
  - "#ACID트랜잭션"
  - "#스키마기반구조"
created: 2025-05-13
type: 기술정리
aliases:
  - 관계형 데이터베이스
  - Relational DB
  - RDB
  - 관계형 데이터 모델
  - SQL 데이터베이스
  - 데이터베이스 정규화
  - ACID 트랜잭션
  - 영속적 데이터 저장
---


# 🗂️ RDBMS (관계형 데이터베이스 관리 시스템)

**RDBMS**는 데이터를 **행(Row)과 열(Column)** 로 구성된 **테이블(Table)** 형태로 관리하는 **관계형 데이터베이스 관리 시스템**이다.  
데이터 간의 관계를 명시적으로 정의하며, **정규화된 데이터 구조**와 **SQL(Structured Query Language)** 을 통해 강력한 쿼리 기능을 제공한다.

---
## RDBMS 주요 특징

- **스키마 기반 구조**: 테이블 정의(DDL)를 통해 데이터 구조를 사전에 고정
- **관계 모델 지원**: 여러 테이블 간 관계(1:N, N:M 등)를 키(Foreign Key)로 정의 가능
- **SQL 사용**: 복잡한 조회, 필터링, 조인(Join), 집계 등의 쿼리가 가능
- **ACID 보장**: 트랜잭션을 통한 일관성, 무결성 보장
- **디스크 기반 저장**: 데이터를 디스크에 영속적으로 저장

---

## RDBMS 대표 제품

- Open-source: [[MySQL]], [[PostgreSQL]], MariaDB
- Commercial: Oracle DB, Microsoft SQL Server, IBM DB2

---

## RDBMS와 In-Memory Database 비교

| 항목 | RDBMS | In-Memory DB |
|------|--------|---------------|
| 저장 위치 | 디스크 | 메모리(RAM) |
| 쿼리 기능 | SQL로 복잡한 쿼리 지원 | 주로 키-값 단순 접근 |
| 데이터 지속성 | 영구 저장 | 서버 종료 시 기본적으로 소멸 (옵션에 따라 영속화 가능) |
| 성능 | 디스크 I/O 병목 가능성 있음 | 매우 빠름 |
| 대표 목적 | 영속성, 정합성 중심 서비스 | 실시간, 임시 데이터 처리 중심 |

---

## RDBMS 주요 활용 사례

- 사용자 정보, 주문, 결제, 이력 관리 등 **중요한 영속 데이터 처리**
- 회계, ERP, CRM, CMS 등 기업 업무 시스템의 핵심 저장소
- 데이터 간 관계가 복잡하거나 조인이 자주 필요한 경우

---

## 관련 문서

- [[In-Memory Database (IMDB)]]
- [[Redis]]
- [[Key-Value Store]]
- [[RDBMS vs Redis]]
