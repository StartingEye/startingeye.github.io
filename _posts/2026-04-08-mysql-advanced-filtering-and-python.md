---
layout: post
title: "[MySQL]자주 쓰는 자료형과 기법, Python 연동 검색"
date: 2026-04-08
categories: [MySQL, Python, 실습]
tags: [mysql, python, datatype, like, between, input, 데이터베이스, beginner]
---

# 1. 자주 쓰는 MySQL 자료형 요약

데이터베이스 테이블 생성을 위해 꼭 알아야 할 대표적인 자료형입니다.

| 자료형 | 설명 |
| :--- | :--- |
| `INT` | 정수 |
| `VARCHAR(n)` | 가변 길이 문자열 (예: 이메일, 이름) |
| `TEXT` | 긴 문자열 (예: 본문, 설명) |
| `DECIMAL(p,s)` | 정확한 소수점 데이터 (예: 가격, 할인율) |
| `DATE` | 날짜 (YYYY-MM-DD) |
| `DATETIME` | 날짜와 시간 (YYYY-MM-DD HH:MM:SS) |

---

# 2. 고급 조건 필터링: LIKE 와 BETWEEN

기본적인 비교 연산자(`=`, `>=`, `<`) 외에, 패턴과 범위를 지정하는 강력한 문법입니다.

## 2.1 문자열 패턴 검색 (`LIKE`)
특정 단어가 포함된 데이터를 검색할 때 사용합니다.
- `%`: 글자 0개 이상을 의미 (무엇이든 올 수 있음)
- `_`: 딱 1글자를 분량으로 의미

```sql
-- '갤럭시'로 시작하는 제품 찾기
SELECT id, name, price FROM products WHERE name LIKE '갤럭시%';

-- 이름 어디서든 '프로'가 들어간 제품 찾기
SELECT id, name, price FROM products WHERE name LIKE '%프로%';

-- 설명에 '이어폰'이 포함되어 있는 제품 조회
SELECT id, name, description FROM products WHERE description LIKE '%이어폰%';
```

## 2.2 범위 검색 (`BETWEEN`)
특정 범위 내의 데이터를 구간으로 편하게 검색할 수 있습니다.
(`>= AND <=`와 완벽히 동일하게 동작합니다.)

```sql
-- 가격이 10000 이상 200000 이하인 제품
SELECT id, name, price FROM products WHERE price BETWEEN 10000 AND 200000;

-- 2026년 3월 2일부터 3월 6일 사이에 출시된 제품 찾기
SELECT id, name, release_date FROM products WHERE release_date BETWEEN '2026-03-02' AND '2026-03-06';
```

---

# 3. Python과 연동하기: `input()` 을 활용한 동적 검색

Python의 `mysql.connector` 모듈을 사용하면 사용자 입력값(`input()`)을 받아 동적으로 데이터베이스를 조회할 수 있습니다.
사용자의 검색어나 지정 가격 대역을 데이터 형식에 맞게 바인딩하여 쿼리하는 예제입니다.

## 💻 실행 예제: 상품 검색 프로그램 (`python step4_4_input_search.py`)

```python
import mysql.connector

# 1. DB 접속 정보 셋팅
conn = mysql.connector.connect(
    host="localhost",
    user="root",
    password="비밀번호",       # 본인 환경에 맞춰 수정
    database="datatype_db", # 사용할 DB
    charset="utf8mb4"
)
cursor = conn.cursor()

print("=== 상품 검색 프로그램 ===")
print("1. 이름 검색 (LIKE)")
print("2. 가격 범위 검색 (BETWEEN)")
menu = input("번호 입력 (1 또는 2): ").strip()

if menu == "1":
    keyword = input("검색어 입력: ").strip()
    
    # %s 예약어를 이용한 파라미터 바인딩으로 안전한 검색 (LIKE 활용 실습)
    sql = "SELECT id, name, price FROM products WHERE name LIKE %s"
    value = (f"%{keyword}%",)
    
    cursor.execute(sql, value)
    rows = cursor.fetchall()

    print(f"\n[이름 검색 결과: {keyword}]")
    if rows:
        for row in rows:
            print(row)
    else:
        print("검색 결과가 없습니다.")

elif menu == "2":
    min_price = input("최소 가격: ").strip()
    max_price = input("최대 가격: ").strip()

    if not min_price.isdigit() or not max_price.isdigit():
        print("가격은 숫자로 입력해야 합니다.")
    else:
        # 두 개의 입력값을 튜플로 매핑 (BETWEEN 활용 실습)
        sql = "SELECT id, name, price FROM products WHERE price BETWEEN %s AND %s"
        value = (int(min_price), int(max_price))
        
        cursor.execute(sql, value)
        rows = cursor.fetchall()
        
        print(f"\n[가격 검색 결과: {min_price} ~ {max_price}]")
        if rows:
            for row in rows:
                print(row)
        else:
            print("검색 결과가 없습니다.")

else:
    print("1 또는 2만 입력하세요.")

# 사용 자원 정리 (매우 중요)
cursor.close()
conn.close()
print("\n프로그램 종료")
```

> **💻 [실행 결과 화면]**
> ![](/assets/img/posts/2026-04-08-mysql-advanced-filtering-and-python/terminal-execution.png)

---
💡 **마무리 요약**
원활한 데이터 관리를 위해서는 알맞은 **자료형 지정**과, 찾고 싶은 데이터를 정확하게 집어내는 **LIKE 및 BETWEEN** 문법 활용이 아주 중요합니다. 더 나아가 Python과 결합하여 동적인 쿼리 바인딩(`%s`)을 사용하는 법을 익히면 기초적인 서버 프로그램 구조의 핵심 뼈대를 구현할 수 있습니다!
