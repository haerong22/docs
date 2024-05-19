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

---
### Switch Expression
- 자바 12 preview, 자바 14 정식 기능
- switch 문이 결과 값을 가지게 됨
```java
private String calculateGradeAfter(int score) {  
    return switch (score) {  
        case 5:  
            yield "A";  
        case 4, 3:  
            yield "B";  
        case 2, 1:  
            yield "C";  
        default:  
            yield "F";  
    };  
}
```
- 반드시 최종 결과가 하나의 값으로 만들어져야 한다.(예외 가능)
- `Enum` 과 사용하기 좋다. (`default` 가 필요 없음)
- `yield` 키워드를 통해 반환한 값을 지정
- colon case label(`:`) 과 `yield` 를 함께 사용
- arrow case label(`->`)  을 통해 `yield` 생략 가능
	- `{}` 사용시에는 `yield` 필수
```java
private String calculateGradeAfter(int score) {  
    return switch (score) {  
        case 5 -> "A";  
        case 4, 3 -> "B";  
        case 2, 1 -> "C";  
        default -> "F";  
    };  
}
```

---
### instanceof Pattern Matching
- 자바 14 preview, 자바 16 정식 기능
- `instanceof`: 어떤 변수가 특정 타입의 인스턴스인지 확인
```java
if (animal instanceof Dog dog) {  
    return dog.bark();  
} else if (animal instanceof Cat cat) {  
    return cat.purr();  
}
```
- `instanceof` pattern matching 을 사용하여 특정 타입을 확인 후 형 변환된 값을 할당 

---
