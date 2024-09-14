### MySQL 에러 구분
- Global Error
	- Server-side & Client-side 에서 공용으로 발생
- Server Error
	- Server-side(MySQL server) 에서만 발생
- Client Error
	- Client-side(driver, connector) 에서만 발생
- 일부 Server Error 는 Client-side로 전달
	- Client-side 에서 보여지는 에러는 Client 또는 Server Error 일 수 있음
	- ex)
		- 존재하지 않는 MySQL 서버에 접속하려고 할때 Client Error(2005) 발생
		- 존재 하지 않는 테이블 조회 시 Server Error(1146) 발생

---
### MySQL 에러 포맷
```mysql
-- ERROR {Error No} ({SQL State}) : {Error Message}
ERROR 1062 (23000) : Duplicate entry 'abc...' for key 'ux_email'
```
#### Error No
- `MySQL` 에서만 유효한 정보
- 에러 번호의 구분
	- 1 ~ 999 : MySQL Global error
	- 1000 ~ 1999 : MySQL Server error
	- 2000 ~ 2999 : MySQL Client(or connector) error
	- 3000 ~ : MySQL Server error
	- MY-010000 ~ : MySQL Server error
- 3500 이후 대역과 MY-010000 이후 대역은 MySQL 8.0 이후 추가됨

#### SQL State
- 5 글자 영문 숫자로 구성
- `ANSI-SQL` 에서 제정한 `Vendor` 비 의존적 에러 코드
- `SQL-STATE` 는 두 파트로 구분
	- 앞 두글자 : 상태값의 분류
		- 00 : 정상
		- 01 : 경고
		- 02 : 레코드 없음
		- HY : `ANSI-SQL` 에서 아직 표준 분류를 하지 않은 상태 (벤더 의존적 상태 값)
		- 나머지는 모두 에러
	- 뒷 세글자 : 주로 숫자 값(가끔 영문) 이며, 각 분류별 상세 에러 코드 값

#### Error Message
- 사람이 인식 할 수 있는 문자열

---
### Error Handling

#### Error Message 이용
- 에러 메시지는 `MySQL` 서버 버전과 스토리지 엔진에 종속적인 경우가 많음(변경 가능)
- 메시지를 에러 처리용으로 사용 비추천

#### Error No 이용
- `MySQL` 의 스토리지 엔진에 종속적인 경우가 많음
- 따라서 `Error no` 도 에러 처리에 적합하지 않을 수 있음

#### SQL State 이용
- `SQL-STATE` 는 `ANSI SQL` 에서 제정한 표준 에러코드이기 때문에 동일함
- `MySQL` 서버의 스토리지 엔진 간의 호환성 제공
- 다른 `Vendor DBMS` 와의 호환성 제공
- `HY` (미분류 상태)로 시작하는 경우 버전 업그레이드시 새로운 `SQL State` 값이 지정될 수 있음