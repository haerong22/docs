### var 변경
- var 을 람다식 매개변수에 적용 가능
- 람다식 매개변수에는 타입을 사용하지 않고 사용할 수 있지만 타입을 명시해야 어노테이션 사용 가능
```java
Consumer<String> c2 = (var x) -> System.out.println(x);
```

---
### String 클래스 업데이트

#### strip() 
- 띄어쓰기 혹은 탭 같은 white space 를 앞 뒤로 제거하는 함수
- `stripLeading()` : 앞 부분의 white space 만 제거
- `stripTrailing()` : 뒷 부분의 white space 만 제거

#### isBlank()
- 특정 문자열이 white space 로만 구성 되어 있으면 `true`

#### lines()
- 개행 문자를 기준으로 문자열을 쪼개 `Stream<String>` 반환

#### repeat()
- 반복 횟수를 파라미터로 받아 주어진 문자열을 반복해 이어붙인 문자열 반환

---
### Collection API 업데이트

#### toArray()
- Collection 을 배열로 쉽게 변경
```java
List<String> strings = List.of("A", "B", "C");  
String[] array = strings.toArray(String[]::new);
```

---
### Predicate 기능 추가

#### not()
- 주어진 조건을 반대로 전환
```java
List<String> strings = List.of("A", " ", "  ");  
  
List<String> result = strings.stream()  
        .filter(Predicate.not(String::isBlank))  
        .collect(Collectors.toList());
```

---
### Files 클래스 

#### readString()
- Path를 받아 파일 내용 전체를 문자열로 읽음
```java
var path = Paths.get("./test.txt");  
String str = Files.readString(path);
```

#### writeString() 
- 문자열 전체를 특정 Path에 쓰기

---
### Http Client 모듈 추가
- 기존의 Http Client - `HttpUrlConnection`
	- `HTTP/2.0`, `HTTP/1.1` 지원 X
	- 비동기 처리 불가

#### HttpClient
- `HTTP/2.0`, `HTTP/1.1` 지원
- `Websocket` 지원
- `CompletableFuture` 를 이용한 비동기 매커니즘 지원
- `HttpRequest` : 요청
- `HttpResponse<T>` : 응답

---
### ZGC(exprimental)
- G1GC 와 유사하게 바둑판 모양으로 메모리를 관리
- G1GC 보다 훨씬 큰 애플리케이션을 대상

---
[예제 코드](https://github.com/haerong22/Study/tree/master/java/java11)