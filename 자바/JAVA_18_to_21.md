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