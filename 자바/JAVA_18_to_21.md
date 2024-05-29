### record pattern
- 자바 19 preview, 자바 21 정식 기능
- `record class` 를 `instance of pattern matching` 과 함께 사용시 내부 필드에 바로 접근
```java
public record Point(double x, double y) {}

public void findDistanceIfPoint(Object object) {  
  
    if (object instanceof Point(double x, double y)) {  
        double dist = Math.hypot(x, y);  
        System.out.printf("원점 으로부터의 거리 : %.3f\n", dist);  
    }  
}
```
- 타입 추론(`var`) 사용 가능
- `record pattern` 은 중첩된 `record class` 에서도 사용 가능
```java
public record Point(double x, double y) {}  

public record Line(Point p1, Point p2) {}

public static void findDistanceIfPoint(Object object) {  
  
    if (object instanceof Line(Point(var x1, var y1), Point(var x2, var y2))) {  
        double dist = Math.hypot(x2 - x1, y2 - y1);  
        System.out.printf("두 점 사이의 거리 : %.3f\n", dist);  
    }  
}
```

---
### switch pattern matching
- 자바 17 preview, 자바 21 정식 기능
- `switch` 선택자 안에 `char`, `byte`, `short`, `int`, `enum` 뿐 아니라 `reference type` 도 가능
- `sealed class` 사용할 경우 `default` 생략 가능
```java
public String sound(Animal animal) {  
    return switch (animal) {  
        case Dog dog -> dog.bark();  
        case Cat cat -> cat.purr();  
    };  
}
```
- `when` 절을 사용해 추가 조건 확인 가능
```java
public String sound(Animal animal) {  
    return switch (animal) {  
        case Dog dog when dog.isQuiet() -> "";  
        case Dog dog -> dog.bark();  
        case Cat cat -> cat.purr();  
    };  
}
```
- 특정 분기에 도달하지 못하는 경우 컴파일 에러 발생(순서 주의)
#### switch 의 NPE 조건 변경
- `switch` 선택자에 `null` 이 들어오고 `case` `null` 이 없을 경우 NPE 발생

---
### Math API 
#### clamp
- 자바 21 추가
- `value` 가 `min` 과 `max` 사이에 있는지 확인 
	- 사이에 있으면 `value`, 더 작으면 `min`, 더 크면 `max` 반환
```java
int result = Math.clamp(10, 5, 15); // 10  
int result2 = Math.clamp(2, 5, 15); // 5  
int result3 = Math.clamp(20, 5, 15); // 15
```

---
### String API

#### indexOf
- 특정 범위 안에 있는 문자/문자열의 위치를 찾을 수 있게 기능 추가

#### splitWithDelimiters
- 자바 21 추가
- 문자열을 나눌 때 `delimiter` 를 포함하여 나눈다.
```java
String str = "A;B;C";  
  
// [A, ;, B, ;, C]  
String[] result = str.splitWithDelimiters(";", -1);
```

---
### StringBuffer / StringBuilder API
#### repeat
- 자바 21 추가
- 특정 문자열이 반복해서 추가
```java
StringBuilder sb = new StringBuilder();  
sb.repeat("ABC ", 3); // ABC ABC ABC 
```

---
### Character API

#### Emoji
- 자바 21 추가
- `codePoint` : `unicode`
```java
public static boolean isEmoji(int codePoint)

public static boolean isEmojiPresentation(int codePoint)

public static boolean isEmojiModifier(int codePoint)

public static boolean isEmojiModifierBase(int codePoint)

public static boolean isEmojiComponent(int codePoint)

public static boolean isExtendedPictographic(int codePoint)
```

---
### Sequenced Collection API
- 자바 21 추가
#### Sequenced Collection Interface
- 순서가 있는 컬렉션의 공통된 기능

| method          | description   |
| --------------- | ------------- |
| `addFirst(E)`   | 맨 앞 원소 추가     |
| `addLast(E)`    | 맨 뒤 원소 추가     |
| `getFirst()`    | 맨 앞 원소 조회     |
| `getLast()`     | 맨 뒤 원소 조회     |
| `removeFirst()` | 맨 앞 원소 삭제     |
| `removeLast()`  | 맨 뒤 원소 삭제     |
| `reversed()`    | 순서 반대의 컬렉션 반환 |
- 원본 데이터가 바뀌면 `reversed` 도 변경, `reversed` 의 데이터가 바뀌면 원본도 변경
- `LinkedHashSet` : 원래 있던 원소를 추가하면 위치가 재조정
- `SortedSet` : 원소 추가 호출 시 `UnsupportedOperationException` 발생

#### SequencedMap Interface

| method              | description             |
| ------------------- | ----------------------- |
| putFirst(K, V)      | 맨 앞 추가                  |
| putLast(K, V)       | 맨 뒤 추가                  |
| firstEntry()        | 첫 Entry 조회(immutable)   |
| lastEntry()         | 마지막 Entry 조회(immutable) |
| pollFirstEntry()    | 첫 Entry 삭제              |
| pollLastEntry()     | 마지막 Entry 삭제            |
| reversed()          | 순서 반대(immutable)        |
| sequencedKeySet()   | key Set(immutable)      |
| sequencedValues()   | values(immutable)       |
| sequencedEntrySet() | Entry Set(immutable)    |
- `LinkedHashMap` : Entry 추가 시 Key 를 기준으로 위치를 재조정
- `SortedMap` : Entry 추가 호출 시 `UnsupportedOperationException` 발생

---

