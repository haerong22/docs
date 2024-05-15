### Preview Feature
- preview feature가 정식 기능으로 포함되는 기간은 보통 두단계 버전(1년)

|       | preview          | experimental | incubating |
| ----- | ---------------- | ------------ | ---------- |
| 적용 대상 | 언어적 기능, JVM      | JVM          | 라이브러리(API) |
| 완성도   | 95%              | 25%          | -          |
| 사용 방법 | --enable-preview | 기능별 플래그      | 모듈 의존성 설정  |
#### experimental
- 실험적이기 때문에 위험하거나 불완전한 기능
- 호환성도 지켜기지 어려움(25% 정도의 완성도)
- experimental 기능 사용을 원하면 전용 플래그 사용

#### incubating
- 모듈 형태로 배포되는 실험용 API
- 호환성이 지켜지지 않을 수 있음
- 모듈과 패키지 앞에 `jdk.incubator` 가 붙음
- 정식모듈 채택시 `jdk.incubator` 가 사라짐

#### preview
- 자바 언어 혹은 JVM과 관련된 새로운 기능
- 완전히 구현 되었지만 피드백을 받기 위한 목적(95% 정도의 완성도)
- 호환되지 않을 수 있어 프로덕션 사용 비권장

---
### Text Block
- 자바 13 preview, 자바 15 정식 기능
- 여러 줄에 걸친 문자열을 만들기 위한 문법
```java
String str = "A\nBC\nDEF"

String textBlock = """  
        A        
        BC        
        DEF""";
```

- 시작하는 `"""` 다음에는 문자가 들어올 수 없다.
- 한 줄로 문자열을 적을 수 없다. (ex: `"""ABC"""`)

- `", '`를 사용하려면 `\"` 대신 `"` 만 사용할 수 있다.
```java
String textBlock = """
	A" 
	BC
	""";
```

- 문자열 끝의 공백은 사라진다.
```java
// 뒷 공백 사용방법

// `replace` 사용
String textBlock = """
	A$$
	BC$
	DEF
	""".replace('$', ' ');

String textBlock = """
	A  |
	BC |
	DEF|
	""".replace("|\n", "\n");

// `octal escape sequence` 사용
String textBlock = """
	A\040\040
	BC\040
	DEF
	""";

// \s 사용 - 공백을 의미
String textBlock = """
	A \s
	BC\s
	DEF
	""";
```

- 한 줄로 된 긴 문자열을 Text Block 으로 쓰고 싶은 경우
```java
// line terminator(\) 사용 - 개행문자 없음
String textBlock = """
	A  \
	BC \
	DEF
	""";
```

- 들여쓰기의 기준선은 문자와 닫히는 `"""` 의 가장 왼쪽