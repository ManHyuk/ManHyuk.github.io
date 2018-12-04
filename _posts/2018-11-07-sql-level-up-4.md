---
layout: post
title: "SQL 레벨업 - 4장 집약과 자르기"
comments: true
description: ""
keywords: ""
---



## 12강 집약



SQL에는 5개의 집약함수가 존재한다.

- COUNT
- SUM
- AVG
- MAX
- MIN

'집약' 이라는 접두사가 붙은 이유는 여러 개의 레코드를 한개의 레코드를 집약하는 기능을 가지고 있기 때문이다.



#### 1. 여러 개의 레코드를 한 개의 레코드로 집약



TB_NonAggTbl

| id   | type | data1 | data2 | data3 | data4 | data5 | data6 |
| ---- | ---- | ----- | ----- | ----- | ----- | ----- | ----- |
| jim  | A    | 100   | 10    |       |       |       |       |
| jim  | B    |       |       | 167   | 77    | 90    |       |
| jim  | C    |       |       |       |       |       | 457   |



```sql
-- 원하는 결과 출력
id  | data1 | data2 | data3 | data4 | data5 | data6
----*-------*-------*-------*-------*-------*------
jim | 100   |  10   | 167   |  77   | 90    | 457
```





데이터 타입별로 data 값을 조회하려면 가장 기본적인 접근법은 WHERE 구문에 type 조건을 걸고 UNION으로 합치는 것이다.

```sql
SELECT id, data1, data2
FROM NonAggTbl
WHERE id = 'jim' AND type = 'A'
UNION
SELECT id, data3, data4, data5
FROM NonAggTbl
WHERE id = 'jim' AND type = 'B'
UNION
SELECT id, data6
FROM NonAggTbl
WHERE id = 'jim' AND type = 'C'
```

UNION으로 여러 개의 쿼리를 머지하는것은 안티 패턴이다.



- CASE 식과 GROUP BY 응용

```sql
SELECT id,
	CASE WHEN tpye = 'A' THEN data1 ELSE NULL END AS data1,
	CASE WHEN tpye = 'A' THEN data2 ELSE NULL END AS data2,
    CASE WHEN tpye = 'B' THEN data3 ELSE NULL END AS data3,
    CASE WHEN tpye = 'B' THEN data4 ELSE NULL END AS data4,
    CASE WHEN tpye = 'B' THEN data5 ELSE NULL END AS data5,
    CASE WHEN tpye = 'C' THEN data6 ELSE NULL END AS data6
FROM NonAggTbl
GROUP BY id;	
```

이 쿼리는 문법 오류가 발생한다. GROUP BY 구로 집약했을때 SELECT 구에 입력할 수 있는 것은 다음과 같은 세 가지 뿐이다.

- 상수
- GROUP BY 구에 사용한 집약키
- 집약 함수

따라서 아래와 같이 수정하면 된다.

```sql
SELECT id,
	MAX(CASE WHEN tpye = 'A' THEN data1 ELSE NULL END) AS data1,
	MAX(CASE WHEN tpye = 'A' THEN data2 ELSE NULL END) AS data2,
	MAX(CASE WHEN tpye = 'B' THEN data3 ELSE NULL END) AS data3,
	MAX(CASE WHEN tpye = 'B' THEN data4 ELSE NULL END) AS data4,
	MAX(CASE WHEN tpye = 'B' THEN data5 ELSE NULL END) AS data5,
	MAX(CASE WHEN tpye = 'C' THEN data6 ELSE NULL END) AS data6,
	FROM NonAggTbl
GROUP BY id;	
```



- 집약, 해시, 정렬

```sql
HashAggregate (cost ~)
	-> Seq Scan on nonaggtbl (~)
```

NonAggTbl 모두 스캔하고 GROUP BY로 집약을 수행하는 단순한 실행 계획이다. 중요한 점은 집약 조작에 해시 알고리즘을 사용하고 있다는 점이다. 집약할때 경우에 따라 정렬을 사용하기도 하지만 최근에는 정렬보다 해시를 사용하는 경우가 많다.



GROUP BY 구에 지정되어 있는 필드를 해시 함수를 사용해 해시키로 변환하고, 같은 해시키를 가진 그룹을 모아 집약하는 방법이다. 해시의 성질상 GROUP BY의 유일성이 높으면 더 효율적으로 작동한다.



하지만 정렬과 해시 모두 메모리를 많이 사용하므로, 충분한 해시용 워킹 메모리가 확보되지 않으면 스왑이 발생한다. 따라서 저장소 위의 파일이 사용되면서 굉장히 느려진다. 



예를들어, 오라클에서 정렬 또는 해시를 위해 PGA라는 메모리 영역을 사용한다. 이때 PGA 크기가 집약 대상 데이터양에 비해 부족하면 일시 영역(저장소)을 사용해 부족한 만큼 채운다.

위 현상을 TEMP 탈락이라 한다. 이 현상이 발생하면 메모리만으로 처리가 끝나느 경우와 비교해 극단적으로 성능이 떨어지게 된다. 메모리와 저장소(일반적으로 디스크)의 접근속도가 굉장히 차이나기 때문이다.









## 13강 자르기



GROUP BY구라는 것은 **자르기** 와 **집약**을 한꺼번에 수행하는 연산이다.



## 1. 자르기와 파티션

- 파티션

GROUP BY 구로 잘라 만든 하나하나의 부분 집합을 수학적으로는 '파티션'이라고 부른다. 서로 중복되는 요소를 가지지 않는 집합이기도 하다. 

```sql
-- 예시
SELECT CASE WHEN age < 20 WHEN '어린이'
			WHEN age BETWWEEN 20 AND 69 THEN '성인'
			WHEN age >= 70 THEN '노인',
		COUNT(*)
FROM Persons
GROUP BY CASE WHEN age < 20 WHEN '어린이'
			WHEN age BETWWEEN 20 AND 69 THEN '성인'
			WHEN age >= 70 THEN '노인'
			ELSE NULL END;
			
			
--- 결과
age | count
----*------
어린이|	1
성인 |	6
노인 |	2
```



자르기의 기준이 되는 키를 GROUP BY 구와 SELECT 구 모두 입력하는것이 포인트다. 

```sql
-- 실행계획
HashAggreagte ( cost ~)
	-> Seq Scan On persons ( cost ~ )
```

GROUP BY 구에서 CASE 식 또는 함수를 사용해도 실행 계획에는 영향이 없다. 단순한 필드가 아니라 필드에 연산을 추가한 식이라면 CPU 연산에 오버헤드가 걸리겠지만, 데이터를 뽑아온 뒤의 이야기 이므로 데이터 접근 경로에는 영향을 주지 않는다.





#### 2. PARTITION BY 구를 사용한 자르기

GROUP BY 구에서 집약 기능을 제외하고 자르기 기능만 남긴것이 PARTITION BY이다. 집약을 제외한 실질적인 기능에는 차이가 없다.







