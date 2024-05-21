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
### Record Class
- 자바 14 preview, 자바 16 정식기능
- 데이터 전달을 위한 클래스(Dto)
```java
public record FruitDtoV2(  
        // 필드(컴포넌트)
        String name,  
        int price,  
        LocalDate date  
) {  
}
```
- 다른 클래스가 `record` `class` 를 상속 불가능
- 컴포넌트에 대한 `private` `final` 필드가 자동 생성
- 필드에 값을 할당하는 생성자 자동 생성
- 필드에 접근할 수 있는 접근자(`getter`) 자동 생성
- `equals()`,` hashCode()`, `toString()` 자동 생성
#### 특징
- 다른 클래스를 상속 불가능
- 인터페이스 구현 가능
- `static` 필드, 함수, 인스턴스 함수 등 생성 가능
- 인스턴스 필드 생성 불가능
- 자동 생성되는 메소드들의 재정의 가능
- `compact constructor`
	- 자동 생성된 생성자 안으로 추가 작업 정의 가능
	- 매개변수를 받지 않음
	- 필드에 값을 할당 할 수 없음
```java
// compact constructor  
public FruitDtoV2 {  
    System.out.println("생성자 호출!!");  
    if (price < 0 ) {  
        throw new IllegalArgumentException("과일의 가격은 양수 입니다.");  
    }  
}
```
- 컴포넌트에 어노테이션을 작성할 경우 클래스 안의 필드, 생성자의 매개변수, 필드에 접근하는 메소드 모두에 어노테이션을 작성한 것과 같다.
	- 특정 요소에 강제하려면 `@Target` 메타 어노테이션 활용
- `Jackson 2.12.0` 이상에서 사용 가능(Spring Boot 2.5.x 이상)

---
### Sealed Class / Interface
- 자바 15 preview, 자바 17 정식 기능
- 하위 클래스를 지정된 클래스로만 제한
- 추상 클래스나 인터페이스를 만들 때 하위 클래스를 제한하고 싶을 때 사용
```java
public sealed abstract class Animal permits Dog, Cat {  
}

public final class Cat extends Animal {  
  
    public String purr() {  
        return "고양이 야옹~";  
    }  
}

public final class Dog extends Animal {  
  
    public String bark() {  
        return "강아지 멍멍";  
    }  
}
```
- 상위 클래스에 `sealed` 키워드를 작성하고 `permits` 키워드로 하위 클래스를 지정
- 하위 클래스에는 `final`, `sealed`, `non-sealed` 셋 중 하나의 키워드를 작성
	- `final` : 재상속 불가능
	- `sealed` : 상속 가능, sealed class 이므로 하위 클래스 지정
	- `non-sealed` : 상속 가능, 하위타입 추적 불가능
- `named module` 인 경우는 부모와 같은 모듈에 있어야 함.
- `unnamed module` 인 경우는 부모와 같은 패키지에 있어야 함.
- 한 파일에 부모와 자식 모두 있다면 `permits` 키워드 생략 가능

---
