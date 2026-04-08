---
layout: post
title: "[MySQL] 데이터 필터링 기초 요약 (WHERE, AND, OR)"
date: 2026-04-08
categories: [MySQL, 실습]
tags: [mysql, where, and, or, 데이터베이스, 필터링, beginner]
---

# 1. 자료형 요약표

데이터베이스에서 데이터를 저장할 때, 목적에 알맞은 자료형을 선택하는 것이 중요합니다.

| 자료형 | 설명 |
| :--- | :--- |
| `INT` | 정수 데이터 |
| `VARCHAR(n)` | 가변 길이 문자열 |
| `TEXT` | 긴 문자열 |
| `DECIMAL(p,s)` | 정확한 소수점 데이터 (돈, 세금, 할인율 등 오차가 없어야 할 때 사용) |
| `FLOAT` / `DOUBLE` | 대략적인 실수 계산에 사용 (미세한 계산 오차가 발생할 수 있음) |
| `DATE` | 날짜 (YYYY-MM-DD) |
| `DATETIME` | 날짜와 시간 |

---

# 2. WHERE 기본 실습

`WHERE` 절을 사용하여 원하는 데이터만 필터링하여 조회할 수 있습니다. 예를 들어 80점 이상인 학생, 혹은 특정 도시에 사는 학생만 고를 수 있습니다.

```sql
-- 점수가 80점 이상인 학생 조회
SELECT * FROM students WHERE score >= 80;

-- 도시가 '서울'인 학생만 조회
SELECT * FROM students WHERE city = '서울';

-- 2학년 학생만 조회
SELECT * FROM students WHERE grade = 2;
```

---

# 3. AND / OR 조건 결합

조건이 여러 개일 때는 `AND`나 `OR`를 사용하여 복합적인 필터링을 할 수 있습니다.

## 3.1. AND: 모든 조건을 만족해야 할 때
```sql
-- 도시가 '서울'이면서 수학 점수가 80점 이상인 학생
SELECT * FROM students 
WHERE city = '서울' AND score_math >= 80;

-- 2학년이면서 데이터베이스 점수가 85점 이상인 학생의 특정 칼럼 조회
SELECT id, name, grade, score_db 
FROM students 
WHERE grade = 2 AND score_db >= 85;
```

## 3.2. OR: 조건 중 하나라도 만족하면 될 때
```sql
-- 도시가 '서울'이거나 '부산'인 학생
SELECT id, name, city 
FROM students 
WHERE city = '서울' OR city = '부산';

-- 수학 점수 또는 데이터베이스 점수가 90점 이상인 학생
SELECT id, name, score_math, score_db 
FROM students 
WHERE score_math >= 90 OR score_db >= 90;
```

## 3.3. AND + OR 조합 
조건이 복잡할 때는 괄호 `()`를 사용하여 우선순위를 명확히 해야 합니다.
```sql
-- 2학년이고, (도시가 '서울' 또는 '부산')인 학생
SELECT id, name, grade, city 
FROM students 
WHERE grade = 2 AND (city = '서울' OR city = '부산');

-- (수학과 DB 점수가 모두 80점 이상)이거나, (3학년)인 학생
SELECT id, name, grade, score_math, score_db 
FROM students 
WHERE (score_math >= 80 AND score_db >= 80) OR grade = 3;
```

---
💡 **마무리 요약**
특정 조건으로 데이터를 추출할 때는 `WHERE` 절을 사용합니다. 다중 조건이 필요할 때는 `AND`(모두 만족), `OR`(하나라도 만족)를 사용하며, 같이 사용할 때는 괄호 `()`로 명확히 논리 흐름을 지정하는 것이 좋습니다!
