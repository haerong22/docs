
### COUNT(\*) 

#### 성능
```sql
SELECT COUNT(*) WHERE ix_fd='A' AND non_ix_fd='B';

SELECT * WHERE ix_fd='A' AND non_ix_fd='B';
```
- 두 쿼리의 성능 차이
	- `ix_fd` 컬럼은 인덱스, `non_ix_fd` 컬럼은 인덱스X
	- `ix_fd` 컬럼의 인덱스를 이용해 대상 레코드를 찾아 데이터 파일에서 레코드를 조회하여 `non_ix_fd` 컬럼 값 비교
	- 성능은 거의 동일  
	- 그러나 일반적으로 `SELECT (*)`은 LIMIT 와 동시에 사용되지만 `SELECT COUNT(*)`는 LIMIT 없이 사용하므로 더 느려질 수 있음
#### 성능 개선
- `Covering Index`를 활용하여 성능 개선
	- 하지만 모든 쿼리를 `Covering Index` 로 튜닝하는 것은 적절 하지 않음
```sql
-- Covering Index
SELECT COUNT(*) WHERE ix_fd1=? AND ix_fd2=?;
SELECT COUNT(id_fd2) WHERE id_fd1=?

-- Non-Covering Index
SELECT COUNT(*) WHERE ix_fd=? AND non_ix_fd=?;
SELECT COUNT(non_ix_fd) WHERE id_fd=?
```
- 최고의 튜닝은 쿼리 자체를 제거
	- 전체 결과 건수 확인 쿼리를 제거
	- 페이지 번호 없이 이전/이후 로 페이지 이동
- 쿼리를 제거할 수 없다면 대략적 건수 활용
	- 부분 레코드 건수 조회
		- `SELECT COUNT(*) FROM (SELECT 1 FROM table LIMIT 200) t;`
	- 임의의 페이지 번호 표기
		- 첫 페이지에서 10개 페이지 표시 후 실제 해당 페이지로 이동하면서 페이지 번호 보정
	- 통계 활용 정보
		- 쿼리 조건이 없는 경우 테이블 통계 활용
			- `SELECT TABLE_ROWS as rows FROM INFORMATION_SCHEMA.tables WHERE schema_name=? AND table_name=?`
		- 쿼리 조건이 있는 경우 실행 계획 활용
			- 정확도 낮음
			- 조인이나 서브쿼리 사용시 계산 난이도 높음
		- 성능은 빠르지만 페이지 이동하면서 보정 필요
- 제거 대상
	- `WHERE` 조건 없는 `COUNT(*)`
	- `WHERE` 조건에 일치하는 레코드 건수가 많은 `COUNT(*)`
- 인덱스를 활용하여 최적화 대상
	- 정확한 `COUNT(*)`가 필요한 경우
	- `COUNT(*)` 대상 건수가 소량인 경우
	- `WHERE` 조건이 인덱스로 처리 될 수 있는 경우

---
### COUNT(\*) vs COUNT(DISTINCT)
- `COUNT(*)`는 레코드 건수만 확인
- `COUNT(DISTINCT)`는 중복된 레코드를 제거 하기 위해 임시테이블을 생성
	- 임시 테이블로 값을 복사하기 위해 임시 테이블에서 중복 된 값이 있는지 SELECT 후 INSERT 
	- 최종 임시 테이블의 레코드 수를 반환
	- 레코드 건수가 매우 많다면 임시 테이블 작업을 메모리에서 하지 않고 디스크로 옮겨서 작업