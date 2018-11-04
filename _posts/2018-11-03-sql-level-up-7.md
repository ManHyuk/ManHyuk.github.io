---
layout: post
title: "SQL 레벨업 - 7장 조건 분기, 집합 연산, 윈도우 함수, 갱신"
comments: false
description: ""
keywords: ""
---


## 조건 분기, 집합 연산, 윈도우 함수, 갱신


#### - SQL과 조건 분기 -

SQL은 코드를 절차적으로 기술하는 것이 아니다. '문장'단위가 아닌 '식' 단위다.
이런 식의 분기를 실현하는 기능이 **CASE** 이다.

- CASE 식의 구문

CASE 식의 구문에는 '단순 CASE 식'과 '검색 CASE 식'이라는 두 종류가 있다. 검색 CASE 식은 단순 CASE 식의 기능을 모두 포함하고 있다.

```sql
CASE WHEN [평가식] THEN [식]
     WHEN [평가식] THEN [식]
     WHEN [평가식] THEN [식]
       -- 생략
     ELSE [식]
END
```

WHEN 구의 평가식이란 '필드 = 값'처럼 조건을 지정하는 식을 말한다.


- CASE 식의 작동
CASE 식의 작동은 절차 지향형 프로그래밍 언어의 switch 조건문과 비슷하다. 절차 지향형 언어의 조건 분기와 SQL 조건 분기 사이의 가장 큰 차이점은 **리턴 값** 이다. 절차 지향형 언어의 조건 분기는 문장을 실행하고 딱히 리턴하지는 않는다. 반면 SQL의 조건 분기는 특정한 값(상수)를 리턴한다.

CASE 식은 식을 적을 수 있는 곳이라면 어디든 적용 가능하다.
> SELECT, WHERE, GROUP BY, HAVING, ORDERED BY 등등

#### - SQL의 집합 연산 -

- UNION으로 합집합 구하기

집합 연산의 기본은 합집합과 교집합이다. WHERE 구에서 합집합을 OR, 교집합을 AND를 사용했다. 하지만 집합 연산에서 합집합은 **UNION** 을 사용한다.

특이점은 두 테이블의 중복을 제거한뒤 결과값을 출력한다. 중복을 허용하려면 **UNION ALL** 을 사용한다.

- INTERSECT로 교집합 구하기

UNION과 마찬가지로 중복된 것이 있다면 해당 레코드는 제외된다.


- EXCEPT로 차집합 구하기

EXCEPT에는 UNION과 INTERSECT에 없는 주의사항이 있다. UNION과 INTERSECT는 순서에 관계없이 결과 값이 같지만 EXCEPT는 순서에 따라 결과가 다르다. 이는 사칙연산과 같은 이치이다.


#### - 윈도우 함수 -

윈도우 함수의 특징을 한마디로 정리하면 '집약 기능이 없는 GROUP BY 구' 이다.

GROUP BY 구는 필드로 테이블을 자르고, 이어서 잘라진 조각 개수만큼의 레코드 수를 더해서 출력한다.

```sql
SELECT address, COUNT(*)
FROM Address
GROUP BY address;

-- 결과
address | count
--------*------
서울시 | 3
인천시 | 2
부산시 | 1
```

반면에 윈도우함수는 'PARTITION BY'구로 수행 한다. 차이점은 테이블을 자른 후에 집약하지 않으므로 출력 결과의 레코드 수가 입력되는 테이블의 레코드 수와 같다는 것이다.

```sql
SELECT address,
  COUNT(*) OVER(PARTITION BY address)
FROM Address;


-- 결과
address | count
--------*------
서울시 | 3
서울시 | 3
서울시 | 3
인천시 | 2
인천시 | 2
부산시 | 1
```

지역별 사람의 수는 양쪽 모두 똑같지만 출력되는 레코드의 수가 다르다. 윈도우 함수는 GROUP BY에서 자르기 기능만 있는것이다.

윈도우 함수로 사용할 수 있는 함수는 COUNT 또는 SUM같은 일반함수 이외에도 RANK, ROW_NUMBER등이 있다.




#### - 트랜잭션과 갱신 -

SQL은 'Structured Query Language'의 약자이다. 즉 데이터 검색을 중심으로 수행하기 위한 언어이다. 데이터를 갱신하는것은 부가적인 기능이다.

SQL의 갱신은 3종류로 분류된다.
1. 삽입 (Insert)
2. 제거 (Delete)
3. 갱신 (Update)

이외에도 1과 3을 합친 머지(Merge)도 존재한다.

RDB는 데이터를 테이블에 보관한다. RDB에서 데이터를 등록하는 단위는 레코드(행 또는 row)다. 삽입할때 INSERT 구문을 사용한다.

```sql
INSERT INTO [테이블 명] ([필드1], [필드2], [필드3] ... )
VALUES ([값1, [값2], [값3] ...);
```

INSERT 구문은 기본적으로 레코드를 하나씩 삽입한다 만약 100개의 레코드를 삽입해야 한다면 multi-row insert를 사용하면 된다.
