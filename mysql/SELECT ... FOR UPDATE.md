### SELECT(Repeatable-read)
- 순수 `SELECT`는 트랜잭션 가시 범위의 데이터 반환
- Non-Locking consistent read(MVCC)
	- 잠금을 걸지 않고 일관된 데이터 읽기
	- MySQL 서버에서는 데이터가 변경되면 항상 변경 전 버전의 레코드를 `undo` 로그를 백업
	- 다른 세션에서 변경 중인 데이터를 읽으려고 하면 `undo` 로그 데이터를 읽어서 반환

---
### SELECT ... FOR \[ UPDATE | SHARE \] (Repeatable-read)
- 격리 수준과 무관하게 항상 최신 커밋 데이터 조회
- 단순 `SELECT` 와 다른 결과 반환 가능
#### SELECT ... FOR UPDATE(`XLock`)
- `FOR UPDATE` 키워드로 `SELECT` 하면 데이터 변경의 의도를 가지고 `SELECT` 문을 수행한다고 판단하여 `XLock` 을 걸고 레코드 조회
- 하나의 트랜잭션에서 `FOR UPDATE` 키워드로 레코드를 읽게되면 더이상 `XLock` 을 걸 수 없음
- 따라서 다른 트랜잭션에서  `FOR UPDATE` 키워드로 레코드를 읽으려고 하면 해당 트랜잭션이 종료 될 때까지 대기 
```mysql
-- 잔고가 100 이상이면 차감
BEGIN;
SELECT * FROM wallet WHERE user_id='A' FOR UPDATE;

if(balance >= 100) {
	UPDATE wallet SET balance = #{current_balance} - 100 WHERE user_id=?;
	COMMIT;
} else {
	ROLLBACK;
}
```
#### SELECT ... FOR UPDATE 튜닝
- 굳이 필요하지 않다면 `FOR UPDATE` 구문은 제거하는 것이 좋음
- `FOR UPDATE`구문이 성능에 특별히 안 좋다기 보다는 `SQL` 문장을 하나라도 줄이는 것이 성능 튜닝에 도움
- `FOR UPDATE` 구문으로 레코드에 잠금을 걸고 다른 원격 서버로의 처리를 요청하는 경우 원격 서버의 응답이 느려지면 트랜잭션이 계속 열린 상태로 레코드의 잠금이 풀리지 않아 다른 트랜잭션의 처리를 막기 때문에 문제가 발생
```mysql
BEGIN;

UPDATE wallet SET balance = #{current_balance} - 100
WHERE user_id=? AND balance >= 100;

if(affected_rows == 1) {
	COMMIT;
} else {
	ROLLBACK;
}
```

- `FOR UPDATE` 구문을 작성할 때 `WHERE` 조건절에 필터링 조건을 넣어서 조회
```mysql
-- 잔고가 10억 이상이면 보너스 적립
BEGIN;
SELECT * FROM wallet WHERE user_id='A' AND balance >= 10억 FOR UPDATE;

if(resultSet.hasNext()) {
	UPDATE wallet SET balance = #{current_balance} + 10000 WHERE user_id=?;
}
```

#### SELECT ... FOR SHARE (`SLock`)
- 부모 테이블의 레코드 삭제 방지
- 부모 테이블의 `SELECT` 와 자식 테이블의 `INSERT` 시점 사이에 부모 삭제 방지
```mysql
BEGIN;
SELECT * FROM article WHERE article_id='A' FOR SHARE;

if(article.canAddComment()) {
	INSERT INTO comment(...) VALUES ('A', ...);
	COMMIT;
} else {
	ROLLBACK;
}
```
- `FOR SHARE` 이 후 UPDATE/DELETE 가 필요한 경우 `FOR SHARE` 자제
	- Lock upgrade 필요(`SLock` -> `XLock`)
	- `Deadlock` 가능성 높음
