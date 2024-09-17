- 원하는 전체 데이터에 대해 부분적으로 나눠서 데이터를 조회 및 처리
- DB 및 애플리케이션 서버의 리소스(cpu/memory/network)사용 효율 증가
- 애플리케이션 처리 시간 단축

### LIMIT & OFFSET
- DB 서버에서 제공하는 `limit & offset` 구문을 사용
- `limit & offset` 사용하는 경우 오히려 DBMS 서버에 더 많은 부하를 발생
```sql
SELECT * FROM table1 LIMIT 500 OFFSET 0;
SELECT * FROM table1 LIMIT 500 OFFSET N;
```
- DBMS 에서 순차적으로 레코드를 읽지 않고 지정된 `offset` 이후 데이터만 바로 가져올 수 없음
- 결국 `limit & offset` 구문을 사용하는 경우 쿼리 실행 횟수가 늘어날 수록 점점 더 읽는 데이터가 많아지고 응답시간이 길어짐
	- 500건씩 N번 조회 시 최종적으로 `(1*500) + (2*500) + ... + (N*500)` 건의 레코드 읽기가 발생
	- `limit & offset` 을 사용하지 않고 한번에 모두 읽어서 가져가는 것보다 더 많은 데이터를 처리하게 됨

---
### 범위 기반 방식
- 날짜 기간이나 숫자 범위로 나눠서 데이터를 조회
- 매 쿼리 실행 시 `WHERE` 절에서 조회 범위를 직접 지정, `LIMIT` 절 사용 X
- 주로 배치 작업 등에서 데이블의 전체 데이터를 일정한 날짜/숫자 범위로 나눠서 조회할 때 사용
- 쿼리에서 사용되는 조회 조건도 단순하고 여러 번 쿼리를 나누어 실행하더라도 사용하는 쿼리 형태 동일
```sql
-- 숫자인 id 값을 바탕으로 범위를 나눠 데이터 조회
SELECT * 
FROM users 
WHERE id > 0 AND id <= 5000;

-- 날짜 기준으로 나눠서 조회
SELECT *
FROM payments
WHERE finished_at >= '2024-01-01' AND finished_at < '2024-01-02';
```

---
### 데이터 개수 기반 방식
- 지정된 데이터 건수만큼 결과 데이터를 반환
- 배치보다는 주로 서비스 단에서 많이 사용되는 방식으로 `ORDER BY & LIMIT` 절 사용
- 처음 쿼리를 실행 할 때(1회차)와 그 이후 쿼리를 실행할 때(N회차) 쿼리 형태가 달라짐
- 쿼리의 `WHERE` 절에서 사용되는 조건 타입에 따라서 N회차 실행 시의 쿼리 형태도 달라짐

#### 동등 조건 사용 시
```sql
CREATE TABLE payments(
	id int NOT NULL AUTO_INCREMENT,
	user_id int NOT NULL,
	... ,
	PRIMARY KEY (id),
	KEY ix_userid_id (user_id, id)
);

-- 1회차
SELECT *
FROM payments
WHERE user_id = ?
ORDER BY id
LIMIT 30;

-- N회차
SELECT *
FROM payments
WHERE user_id = ? AND id > {이전 데이터의 마지막 id 값}
ORDER BY id
LIMIT 30;
```
- `ORDER BY` 절에는 각각의 데이터를 식별할 수 있는 식별자 컬럼이 반드시 포함

#### 범위 조건 사용 시
```sql
CREATE TABLE payments(
	id int NOT NULL AUTO_INCREMENT,
	user_id int NOT NULL,
	finished_at datetime NOT NULL,
	... ,
	PRIMARY KEY (id),
	KEY ix_finishedat_id (finished_at, id)
);

-- 1회차
SELECT *
FROM payments
WHERE finished_at >= '{시작 날짜}' AND finished_at < '{종료 날짜}'
ORDER BY finished_at, id
LIMIT 30;

-- N회차
SELECT *
FROM payments
WHERE (
		(finished_at = '{이전 마지막 finished_at}' AND id > {이전 데이터 마지막 id}) 
		OR (finished_at > '{이전 마지막 finished_at}' AND finished_at < '{종료 날짜}')
	   )
ORDER BY finished_at, id
LIMIT 30;

-- 범위 조건 컬럼의 값 순서와 ID 컬럼의 값 순서가 동일 한 경우
SELECT *
FROM user_logs
WHERE created_at >='{이전 마지막 created_at}'
	AND created_at < '{종료 날짜}'
	AND id > {이전 데이터 마지막 id}
ORDER BY created_at, id
LIMIT 30;
```
- `ORDER BY` 절에 범위 조건 컬럼인 `finished_at` 이 포함
	- `id` 컬럼만 명시하는 경우 조건을 만족하는 데이터들을 모두 읽어들인 후 `id`로 정렬하고 지정된 건수만큼 반환
	- `finished_at`컬럼을 선두에 명시하면 `(finished_at, id)` 인덱스를 사용해서 정렬 작업 없이 원하는 건수만큼 순차적으로 데이터를 읽을 수 있으므로 처리 효율 향상
