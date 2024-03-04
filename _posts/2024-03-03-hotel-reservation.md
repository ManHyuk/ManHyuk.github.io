---
layout: post
title: "7장 호텔 예약 시스템"
comments: true
description: ""
keywords: ""
draft: true
---




### 디비 선택 
관계형 데이터베이스는 읽기 빈도가 쓰기 연산에 비해 높은 작업 흐름을 잘 지원한다

호텔 웹사이트는 호텔을 예약하는 사용자보다 방문하는 사용자의 수가 더 많다.

따라서 쓰기 연산에 최적화된 NoSQL 보다 관계형 데이터베이스가 더 적절하다

관계형 데이터베이스는  ACID 속성을 보장한다.

따라서 잔액이 마이너스 되는 문제, 이중 청구문제, 이중 예약 문제등을 방지하기 수월하다



### 개략적 설계안

MSA 아키텍처를 사용한다. 

![msa](/images/hotel/msa.jfif)

각각의 서비스 간 통신에는 gRPC와 같은 방법을 사용하곤 한다.


### 설계

예약 프로세스의 입출력은 다음과 같다.


입력: startDate, endDate, roomTypeId, hotelId, numberOfRoomsToReserve

출력: 해당 유형 객실에 여유가 있고 사용자가 예약 가능한 상태면 true,  그렇지 않으면 false 를 반환


1. 주어진 기간에 해당하는 레코드를 구한다

```sql
SELECT datee, total_inventory, total_reserved
FROM room_type_inventory
WHERE room_type_id = #{roomTypeId} AND hotel_id = #{hotelId}
AND datee BETWEEN #{startDate} AND #{endDate}
```


|  date       | total_inventory  |  total_reserved |  
|-------------|------------------|-----------------|
| 2022-07-01  | 100              | 97              |
| 2022-07-02  | 100              | 96              |
| 2022-07-03  | 100              | 95              |

2. 반환된 각 레코드 마다 다음 조건을 확인한다.

```js
if ((total_reserved + ${numberOfRoomsToReserve} <=  110 * total_inventory)) {
 // 호텔 예약 가능 상태
}
```



### 동시성 문제

위 방법으로 예약은 가능하지만 이중 예약 문제는 해결하지 못한다.

해결하기 위핸 두 가지 문제를 해결해야한다.

1. 같은 사용자가 예약 버튼을 여러번 누를 수 있다.
2. 여러 사용자가 같은 객실을 동시에 예약하려 할 수 있다.


#### 시나리오 1


| reservation_id | hotel_id | room_type_id | start_date | end_date   | status      | guest_id |
|----------------|----------|--------------|------------|------------|-------------|----------|
| 121            | 2        | 3            | 2021-07-01 | 2021-07-04 | pending_pay | guest1   |
| 121            | 2        | 3            | 2021-07-01 | 2021-07-04 | pending_pay | guest1   |



클라이언트 측 구현: 요청을 전송하고 난 다음에 예약 버튼을 비활성화 하는 방법. 하지만 변조를 통해 우회할 수 있다.

멱등 API: 예약 API 요청에 멱등 키를 추가하는 방법이다. 몇 번을 호출해도 같은 결과를 내는  API 를 멱등  API 라고 한다.
reservation_id 를 사용해 멱등성을 보장할 수 있다.

![Idempotent](/images/hotel/idempotent.png)



1. 예약 주문서를 만든다. 고객이 정보를 입력하고 '계속' 버튼을 누르면 예약 주문을 생성한다.
2. 고객이 검토할 수 있도록 예약 주문서를 반환한다. 이때  API 는 반환 결과에 `reservation_id` 를 넣는다
![booking](/images/hotel/booking.PNG)

3. 검토가 끝난 예약을 전송한다 이때 요청에도 `reservation_id`가 붙는다.
4. 사용자가 예약 완료 버튼을 한 번 더 누르게 되면 `reservation_id` 는 PK 이기 때문에 새로운 레코드는 생성되지 않는다.


#### 시나리오 2

여러 사용자가 잔여 객실이 하나밖에 없는 유형의 객실을 동시에 예약하려는 경우


![cc](/images/hotel/cc.png)

1. 디비 트랜잭션이 없는 경우를 가정, 사용자1과 사용자2가같은 유형의 객실을 예약하려고 할때
2. 트랜잭션 1은 `(total_reserved + rooms_to_book) <= total_inventory` 인지 검사
3. 트랜잭션 2도 `(total_reserved + rooms_to_book) <= total_inventory` 인지 검사
4. 트랜잭션 1이 먼저 객실을 예약하고 현황을 갱신하여 reserved_room 은 100이 된다
5. 그 후 트랜잭션 2가 예약하게 된다면 트랜잭션2의 관점에서 total_reserved 의 값은 여전히 99이다



위 문제를 해결하려면 어떤 형태로든 락을 활용해야 한다.


- 비관적 락 (선점 잠금, Pessimistic Lock)
- 낙관적 락 (비선점 잠금, Optimistic Lock)
- 데이터베이스 제약 조건



#### 비관적 락

사용자가 레코드를 갱신하려고 하는 순간 즉시 락을 걸어 동시 업데이트를 방지하는 방법이다.

해당 레코드를 갱신하려는 다른 사용자는 먼저 락을 건 사용자가 변경을 마치고 락을 해제할때까지 기다려야 한다.

MySQL 의 경우 `SELECT ... FOR UPDATE` 를 실행하면 레코드에 락이 걸린다.

장점 : 
1. 애플리케이션이 변경 중이거나 변경이 끝난 데이터를 갱신하는 일을 막을 수 있다.
2. 구현이 쉽다

단점 :
1. 여러 레코드에 락을 걸면 교착 상태가 발생할 수 있다.
2. 확장성이 낮다. 트랜잭션이 너무 오랫동안 락을 해제 하지 않는다면 성능에 영향을 줄 수 있다.


#### 낙관적 락

낙관적 락은 일반적으로 버전 번호, 타임 스탬프 두 가지 방법으로 구현한다.

1. 테이블에 version 이라는 컬럼을 추가한다.
2. 사용자가 수정하기 전에 앱에서 해당 레코드의 버전을 읽는다.
3. 레코드를 갱신할때 버전에 1을 더한 다음 디비에 다시 기록한다.
4. 이때 유효성 검사를 한다. 즉 다음 버전 번호는 현재 버전 번호보다 1만큼 큰 값이어야 한다. 이 검사가 실패한다면 즉시 중지한다.



장점:
1. 낙관적 락은 일반적으로 비관적 락보다 빠르다. 디비에 락을 걸지 않기 때문이다.
2. 경쟁이 치열하지 않은 상황에 적합

단점:
1. 경쟁이 치열한 상황에서는 성능이 좋지 않다






