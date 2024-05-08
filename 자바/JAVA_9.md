### 1. 자바 플랫폼 모듈 시스템
- Java 9에서는 모듈 시스템이 도입되어 모듈화된 소프트웨어를 더 쉽게 개발하고 관리할 수 있다.

자바 플랫폼 모듈 시스템 (JPMS) VS Gradle 같은 빌드 툴의 모듈
- JPMS 의 모듈 설정과 빌드툴 모듈 설정을 함께 사용할 수 있다.

1. 빌드 툴 없이 모듈 구성이 가능하다.
	-> JDK 자체가 여러 모듈로 나눠지고, 특정 모듈만 선택해서 다운로드 할 수 있다.
	Modular run time image = JRE / JDK
2. 조금 더 세밀한 접근서어 제어가 가능하다.
	-> private 멤버에 대한 리플렉션 오픈 여부도 설정 할 수 있다.

#### 모듈 설정
`src > main > java` 하위에 `module-info.java` 파일을 만들어서 모듈 설정을 할 수 있다.

- 모듈은 기본적으로 다른 모듈에게 API 를 노출하지 않는다.
- 사용되는 모듈은 `exports` 키워드로 특정 패키지를 외부에 노출 할 수 있다.
- `exports ~ to` 키워드로 특정 모듈에만 노출 할 수 있다.

| 키워드                                | 설명                            |
| ---------------------------------- | ----------------------------- |
| requires                           | complie, runtime 의존성          |
| requires static                    | complie                       |
| requires transitive                | complie, runtime 의존성 및 의존성 전파 |
| exports {package path}             | 패키지를 외부로 노출                   |
| exports {package path} to {module} | 패키지를 특정 모듈에만 노출               |

##### 다음과 같이 작성하여 사용할 수 있다.
`module-info.java`
```java
module com.bobby.domain {  
    exports com.bobby.domain;  
    exports com.bobby.domain.admin to com.bobby.admin;  
}

```
```java
module com.bobby.api {  
    requires com.bobby.domain;  
}
```


Named module
- `module-info.java` 를 사용한 모듈 안의 코드

Unnamed module
- `module-info.java` 를 사용하지 않은 모듈 안의 코드

#### Reflection 변경
> Deep reflection : private 멤버까지 조작할 수 있는 리플렉션
> Shallow reflection : 기본적인 자바의 접근 지시어를 준수하는 리플렉션

- 기본적으로 private 멤버에 자유롭게 접근할 수 없도록 변경
- open / opens / opens .. to 키워드를 통해 deep reflection 을 할 수 있다.

| 키워드         | 설명                                |
| ----------- | --------------------------------- |
| open        | 전체 코드에 대한 deep reflection 허가      |
| opens       | 특정 패키지에 대한 deep reflection 허가     |
| opens .. to | 특정 패키지만 특정 모듈에 deep reflection 허가 |
`전체 모듈 open`
```java
open module com.bobby.domain {  
    exports com.bobby.domain;  
}
```

`특정 패키지 open`
- exports 를 하지 않고 open 만 할 경우?
	-> 컴파일 할 때 코드를 import 할 수는 없지만, deep reflection은 정상적으로 사용 가능
```java
module com.bobby.domain {  
    exports com.bobby.domain;  
    opens com.bobby.domain;
}
```

#### 서비스 등록 및 사용
- 코드의 변경 없이 원하는 기능을 갈아끼우는 매커니즘
	-> Spring 의 DI 와 같은 개념
- 기존에는 resources/META-INF/services 안에 파일을 만들어 등록하여 사용
	-> 모듈 시스템을 통해 등록하고 사용이 가능하게 변경

서비스 구현체를 등록하는 방법
- provides ... with ... 키워드로 등록 할 수 있다.

`module-info.java`
```java
module com.bobby.domain {  
    exports com.bobby.domain;  
    exports com.bobby.domain.service;    
  
    provides com.bobby.domain.service.StringRepository with  
            com.bobby.domain.service.MemoryStringRepository,  
            com.bobby.domain.service.DatabaseStringRepository;  
}
```

서비스를 사용하는 방법
- uses 키워드를 통해 사용한다.
`module-info.java`
```java
module com.bobby.api {  
    requires com.bobby.domain;  
  
    uses com.bobby.domain.service.StringRepository;  
}
```


#### 클래스 로더 구성 변경
- 클래스 로더 : `.java` 파일이 컴파일 되어  생성된 `.class` 파일을 JVM이 사용할 수 있도록 가져온다.

| 변경 전                                                | 변경 후                                                                                   |
| --------------------------------------------------- | -------------------------------------------------------------------------------------- |
| BootStrap 클래스 로더<br>- 가장 기본적인 클래스들을 가져온다.           | BootStrap 클래스 로더<br>- java.base 모듈의 클래스들을 가져온다.                                        |
| Extensions 클래스 로더<br>- 추가적인 클래스들을 가져온다.             | Platform 클래스 로더<br>- Bootstrap 클래스 로더가 처리하지 않는 Java SE platform or JCP 모듈의 클래스들을 가져온다. |
| System 클래스 로더<br>- classpath 에서 찾을 수 있는 클래스들을 가져온다. | Application 클래스 로더<br>- Java SE Platform 혹은 JDK 안에 있지 않은 클래스들을 가져온다.                   |

#### JRE, JDK의 구성 변경
- rt.jar, tools.jar 등 JRE / JDK 내부에 있던 몇 가지 파일 삭제
- JRE / JDK 디렉토리 구성 변경
- Modular Image : 필요한 모둘들만 가지고 있는 JRE / JDK 를 구성 할 수 있다.

---

### 2. 추가 기능

#### 확장된 try-with-resources
-  try 로직이 모두 끝나면 해당 자원을 닫아준다.(AutoCloseable 의 구현 필요)
- 기존에는 try 밖에서 만든 자원을 닫을 수 없었다.
	-> 바깥의 final 변수는 또는 사실상 final 인 변수는 닫을 수 있도록 변경
```java
Resource r1 = new Resource("r1");
final Resource r2 = new Resource("r2");

try (r1; r2) {
	...
}
```
#### @SafeVarargs 어노테이션
- `private` 메소드와 사용할 수 있게 변경

#### 익명 inner class 
- 익명 inner class 가 제네릭 클래스라면 `diamond syntax` 를 함께 사용할 수 있게 변경
- `diamond syntax` : 제네릭 클래스 인스턴스화 시 우항 타입 생략

#### Interface
- 인터페이스가 private method 를 가질 수 있게 변경

#### Underscore 네이밍
- underscore 네이밍이 불가능하게 변경

#### Collection 기능 추가
- 정적 팩토리 메소드 of 추가(List, Set, Map)
	- of 로 만들어진 컬렉션은 불변이다.
	- Collections.UnmodifableXXX() 과는 다른 구현체를 사용하며 조금더 메모리 효율적

#### Optional 기능 추가
- ifPresentOrElse
	- 값을 있는 경우, 없는 경우 둘 다 제어 가능
- or
	- 값이 없을 경우 다른 Optional 반환
- stream
	- Optional 안에 값이 있으면 원소 1개의 Stream
	- 값이 없으면 비어있는 Stream

#### Stream 기능 추가
- takeWhile
	- filter와 비슷한 기능
	- false 가 나오는 순간 뒤의 데이터는 모두 버린다.
- dropWhile
	- true 인 경우 데이터를 버리다가 false 를 만나면 모두 남긴다.
- ofNullable
	- null 이면 비어있는 Stream 값이 있으면 원소가 하나인 Stream
- 개선된 iterate
	- 스트림을 생성할 때 조건을 추가 할 수 있다.

#### CompletableFuture 기능 추가
- CompletableFuture 복사 기능
- default Executor를 가져는 기능
- 타임아웃 기능
	- orTimeout : 정해진 시간 안에 작업이 완료되지 않으면 예외 발생
	- completeOrTimeout : 정해진 시간 안에 작업이 완료되지 않으면 정해진 값 반환
- 지연 기능
	- DelayedExecutor

#### Process API 추가
- ProcessHandle : native 프로세스를 제어할 수 있는 인터페이스

프로세스 관련 주요 인터페이스

| 인터페이스              | 설명                |
| ------------------ | ----------------- |
| Process            | 프로세스 자체를 표현       |
| ProcessHandle      | 프로세스를 제어하는 기능들    |
| ProcessHandle.Info | 프로세스 관련 다양한 정보 조회 |

#### Stack-Walking API 추가
- 스택을 훑는 기능

#### 내부 문자열 처리 방식 개선
- 이전에는 UTF16 char 배열을 사용하여 글자 하나당 2바이트 사용
- 9부터 Latin 인 경우 1바이트 UTF16인 경우 2바이트 사용 -> Compact String

#### JDK 버전 스키마 방식 변경
- 메이저.마이너.보안패치 ex -> 9.0.0
- 9.0.1 버전에서 마이너 패치가 이루어지면 9.1.1
	- 보안패치 버전은 유지된다

#### 메모리 및 GC 관련 변경사항
- 기본 GC 가 Parallel GC 에서 G1GC 로 변경 
- 기존 GC 튜닝에 사용되던 CMS GC는 deprecated
- OutOfMemoryError 관련 옵션 추가
	- ExitOnOutOfMemoryError : OOM이 처음 발생하면 JVM 자체를 종료
	- CrashOnOutOfMemoryError : OOM이 처음 발생하면 JVM 크래시를 일으키고 크래시 파일을 만들어 두는 옵션

---

### 3. Flow API
- 리액티브 프로그래밍 지원을 위한 API 추가
- 자바 표준 라이브러리
- 다양한 리액티브 라이브러리간의 호환성이 좋아지고 자바 내부에서도 리액티브 프로그래밍이 가능해짐
#### RxJava vs Reactor vs Flow API

|                  | RxJava       | Reactor        | Flow API      |
| ---------------- | ------------ | -------------- | ------------- |
| JDK 버전           | 8 미만에서 사용 가능 | 8 이상에서만 사용 가능  | 9 이상에서만 사용 가능 |
| 활용               | 클라이언트        | spring webflux | 호환성, 표준 라이브러리 |
| reactive streams | 약간 다름        | 완전히 동일         | 완전히 동일        |

---
[예제 코드](https://github.com/haerong22/Study/tree/master/java/java9)