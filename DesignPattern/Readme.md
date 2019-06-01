## 클래스 설계 원칙

OOP (객체지향 프로그래밍)

S: SRP 단일 책임 원칙

O: OCP 개방-폐쇄 원칙

L: LSP 리스코프 치환 원칙

I: ISP 인터페이스 분리 원칙

D: DIP 의존 역전 원칙

### 1. 단일 책임 원칙 (Single Responsibility Principle)

클래스가 변해야 하는 이유는 단 1개. 책임을 분리하는 것

### 2. 개방 폐쇄 원칙 (Open Closed Principle)

기존 코드를 변경하지 않으면서 기능을 추가할 수 있도록 설계가 되어야 한다.

### 3. 리스코프 치환 원칙 (Liskov Substitution Principle)

서브 타입은 언제나 자신의 기반타입으로 교체할 수 있어야 한다.

자식 클래스는 최소한 자신의 부모 클래스에서 가능한 행위는 수행할 수 있어야 한다.

프로그램에서 부모 클래스의 인스턴스 대신에 자식 클래스의 인스턴스로 대체해도 프로그램의 의미는 변화되지 않는다. 즉, 인터페이스와 클래스 관계, 상위, 하위 클래스들을 얼마나 잘 설계했느냐가 중요하다.

부모 클래스에서 상속받은 메서드들이 자식 클래스에 오버라이드 되지 않게 하면 된다. 

### 4. 인터페이스 분리 원칙 (Interface Segregation Principle)

클라이언트는 자신이 사용하지 않는 메서드에 의존 관계를 맺으면 안된다.

인터페이스를 클라이언트에 특화되도록 분리시키라는 원칙 (SRP와 밀접)

자신이 관심을 갖는 메서드들만 있는 인터페이스를 제공받도록 설계한다.

### 5. 의존 역전 원칙 (DIP dependency Inversion Principe) 

자주 변경되는 concrete 클래스에 의존하지 마라.

의존 관계를 맺을 때 변화하기 쉬운 것 또는 자주 변화하는 것 보다는 변화하기 어려운 것, 거의 변화가 없는 것에 의존한다.

상위로 갈 수록 더 추상적이고 변화할 가능성이 적게 설계 한다.

## 팩토리 패턴

어떤 클래스의 인스턴스를 만들지 서브클래스에서 결정하는 패턴

조건에 따른 객체 생성 부분을 자신이 직접하지 않고 팩토리 클래스에 위임하여 객체를 생성하는 방법이라고 할 수 있다. 이렇게 되면 객체간 결합도가 낮아져서 유지보수에 좋다.

```java
private static final int LINE_VERTEX = 2;
private static final int TRIANGLE_VERTEX = 3;
private static final int RECTANGLE_VERTEX = 4;

public static Figure createFigure(Points points) {
    int size = points.getSize();

    if (size == LINE_VERTEX) {
        return new Line(points);
    }
    if (size == TRIANGLE_VERTEX) {
        return new Triangle(points);
    }
    if (size == RECTANGLE_VERTEX) {
        return new Rectangle(points);
    }
    throw new IllegalArgumentException("Points 형식이 잘못 되었습니다");
}
```

정의해본 팩토리 패턴이다. Points는 좌표의 배열을 가진 객체이다. 

```java
public class Points {
    private final List<Point> points;

    public Points(List<Point> points) {
        checkPointsIsDuplicate(points);
        this.points = points;
    }

    private void checkPointsIsDuplicate(List<Point> points) {
        Set<Point> pointSet = new HashSet<>(points);
        if (pointSet.size() != points.size()) {
            throw new IllegalArgumentException("중복된 좌표는 입력할 수 없습니다");
        }
    }
    // ... 중략
}
```

### 위 팩토리 패턴의 문제점

여러 중첩된 if 문으로 인한 보기 싫음.

Java 8 부터 사용되는 함수형 인터페이스를 사용하여 간결하게 리팩토링 할 수 있다. 람다식에 메서드 또는 생성자의 매개타입으로 사용된다. 

패키지는 java.util.function에 있다.

#### Consumer

매개값이 있고 리턴 값이 없다. 

```java
Consumer<T> 
void accept(T t) // 객체 T를 받아서 소비한다

BiConsumer<T, U>
void accet(T t, U u) // 객체 T와 U를 받아서 소비한다
    
IntConsumer
void accapt(int value) // int 값을 받아서 소비한다
```

#### Supplier

매개 변수는 없고 리턴값이 있다. 단순 공급자의 역할을 한다.

```java
Supplier<T>
T get() // T 객체를 리턴한다

BooleanSupplier
boolean getAsBoolean() // boolean 값을 리턴한다

IntSupplier
int getAsInt() // int 값을 리턴한다
```

#### Function

매개 변수와 리턴값이 둘 다 존재한다.

```java
Function<T, R>
R apply(T t) // 객체 T를 객체 R로 매핑한다
    
BiFunction<T,U,R> 
R apply(T t, U u) // 객체 T와 U를 객체 R로 매핑한다

IntFunction<R>
R apply(int value) // int를 객체 R로 매핑한다
```

#### Predicate

매개 변수가 있고 boolean을 리턴한다.

```java
Predicate<T>
boolean test(T t) // 객체 T를 판별

BiPredicate<T, U> 
boolean test(T t, U u) // 객체 T와 U를 판별