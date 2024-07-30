### 3. Lambda Expressions

람다 표현식은 Java 8의 주요 기능 중 하나로, 함수를 더 간결하고 명확하게 표현할 수 있게 해줍니다. 이 장에서는 람다 표현식의 개념과 사용 방법에 대해 자세히 설명합니다.

#### 3.1. 람다 표현식 간단히 보기

람다 표현식은 익명 함수로, 메서드처럼 매개변수를 가질 수 있고, 실행될 수 있는 코드 블록을 포함합니다. 익명 클래스의 장황함을 줄이고 코드를 더 간결하게 만들어 줍니다. 람다 표현식의 기본 형태는 다음과 같습니다:

```java
(parameters) -> expression
```

또는 여러 문장이 있을 경우 중괄호를 사용합니다:

```java
(parameters) -> { statements; }
```

람다 표현식의 몇 가지 예를 살펴보겠습니다.

#### 3.1.1. 예제: 간단한 람다 표현식

람다 표현식을 사용하여 두 숫자의 합을 구하는 예제를 살펴보겠습니다.

```java
(int a, int b) -> a + b
```

위 예제는 두 개의 정수를 더하여 결과를 반환하는 람다 표현식입니다.

#### 3.1.2. 예제: 매개변수가 없는 람다 표현식

매개변수가 없는 람다 표현식도 가능합니다.

```java
() -> System.out.println("Hello, World!")
```

위 예제는 "Hello, World!"를 출력하는 람다 표현식입니다.

#### 3.1.3. 예제: 여러 문장을 포함하는 람다 표현식

여러 문장을 포함하는 람다 표현식의 예제입니다.

```java
(String s) -> {
    System.out.println(s);
    int len = s.length();
    return len;
}
```

위 예제는 문자열을 출력하고, 문자열의 길이를 반환하는 람다 표현식입니다.

람다 표현식의 기본 개념을 이해했으니, 이제 람다 표현식이 어디에서 어떻게 사용될 수 있는지 살펴보겠습니다.

#### 람다 표현식의 사용처

람다 표현식은 다양한 곳에서 사용될 수 있으며, 특히 자주 사용되는 몇 가지 사례를 소개합니다.

1. **Comparator**:
   - 객체를 정렬할 때 사용됩니다. 예를 들어, 사과 목록을 무게 순으로 정렬할 때 람다 표현식을 사용할 수 있습니다.
   
   ```java
   inventory.sort((Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight()));
   ```

2. **Runnable**:
   - 새로운 스레드를 실행할 때 사용됩니다.
   
   ```java
   Thread t = new Thread(() -> System.out.println("Hello, World!"));
   t.start();
   ```

3. **Listener**:
   - GUI 애플리케이션에서 이벤트 리스너를 정의할 때 사용됩니다.
   
   ```java
   button.setOnAction(event -> System.out.println("Button clicked!"));
   ```

4. **Predicate**:
   - 조건을 테스트할 때 사용됩니다.
   
   ```java
   List<Apple> greenApples = filterApples(inventory, apple -> "green".equals(apple.getColor()));
   ```

5. **Function**:
   - 함수를 정의할 때 사용됩니다.
   
   ```java
   Function<String, Integer> stringLength = s -> s.length();
   ```

람다 표현식은 코드의 간결성을 높이고, 익명 클래스의 장황함을 줄이는 데 큰 도움이 됩니다.

### 3.2. 람다 표현식을 어디서 어떻게 사용할까? (Where and How to Use Lambdas)

람다 표현식은 Java 8에서 제공하는 함수형 프로그래밍 스타일의 핵심 요소로, 다양한 상황에서 유용하게 사용할 수 있습니다. 이 절에서는 람다 표현식을 사용할 수 있는 몇 가지 주요 사례와 이를 효과적으로 활용하는 방법을 설명합니다.

#### 3.2.1. 함수형 인터페이스 (Functional Interfaces)

람다 표현식은 주로 함수형 인터페이스와 함께 사용됩니다. 함수형 인터페이스는 단 하나의 추상 메서드를 가지는 인터페이스입니다. Java 8에서는 `@FunctionalInterface` 어노테이션을 사용하여 함수형 인터페이스를 명시할 수 있습니다. 예를 들어, `Comparator` 인터페이스는 함수형 인터페이스입니다.

```java
@FunctionalInterface
public interface Comparator<T> {
    int compare(T o1, T o2);
}
```

람다 표현식은 이러한 함수형 인터페이스의 인스턴스를 간단하게 생성할 수 있게 합니다. 

#### 3.2.2. 컬렉션 정렬 (Sorting Collections)

람다 표현식을 사용하여 컬렉션을 정렬하는 예제입니다. `Comparator` 함수형 인터페이스를 사용하여 리스트를 정렬할 수 있습니다.

예제: 사과 목록을 무게 순으로 정렬하기

```java
List<Apple> inventory = Arrays.asList(new Apple(80, "green"), new Apple(155, "red"), new Apple(120, "green"));

// 기존 방식
inventory.sort(new Comparator<Apple>() {
    @Override
    public int compare(Apple a1, Apple a2) {
        return a1.getWeight().compareTo(a2.getWeight());
    }
});

// 람다 표현식 방식
inventory.sort((Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight()));
```

#### 3.2.3. Runnable 사용하기

스레드를 생성하고 실행하는 경우에도 람다 표현식을 사용할 수 있습니다. `Runnable` 인터페이스는 함수형 인터페이스이므로, 람다 표현식을 사용하여 더 간결하게 스레드를 생성할 수 있습니다.

예제: 새로운 스레드 실행하기

```java
// 기존 방식
Thread t = new Thread(new Runnable() {
    @Override
    public void run() {
        System.out.println("Hello, World!");
    }
});
t.start();

// 람다 표현식 방식
Thread t = new Thread(() -> System.out.println("Hello, World!"));
t.start();
```

#### 3.2.4. GUI 이벤트 처리

JavaFX와 같은 GUI 애플리케이션에서 버튼 클릭 이벤트를 처리할 때도 람다 표현식을 사용할 수 있습니다.

예제: 버튼 클릭 이벤트 처리

```java
Button button = new Button("Click me");

// 기존 방식
button.setOnAction(new EventHandler<ActionEvent>() {
    @Override
    public void handle(ActionEvent event) {
        System.out.println("Button clicked!");
    }
});

// 람다 표현식 방식
button.setOnAction(event -> System.out.println("Button clicked!"));
```

#### 3.2.5. 필터링 (Filtering)

람다 표현식은 컬렉션을 필터링할 때도 유용하게 사용됩니다. 예를 들어, 특정 조건을 만족하는 사과를 필터링하는 메서드를 작성할 수 있습니다.

예제: 특정 조건에 맞는 사과 필터링

```java
public static List<Apple> filterApples(List<Apple> inventory, Predicate<Apple> predicate) {
    List<Apple> result = new ArrayList<>();
    for (Apple apple : inventory) {
        if (predicate.test(apple)) {
            result.add(apple);
        }
    }
    return result;
}

// 람다 표현식 사용
List<Apple> greenApples = filterApples(inventory, apple -> "green".equals(apple.getColor()));
List<Apple> heavyApples = filterApples(inventory, apple -> apple.getWeight() > 150);
```

#### 3.2.6. 함수 조합 (Function Composition)

람다 표현식을 사용하여 함수를 조합할 수도 있습니다. Java 8에서는 `Function` 인터페이스를 제공하여 함수 조합을 쉽게 할 수 있게 합니다.

예제: 함수 조합

```java
Function<Integer, Integer> f = x -> x + 1;
Function<Integer, Integer> g = x -> x * 2;

Function<Integer, Integer> h = f.andThen(g); // 먼저 f를 적용한 후 g를 적용
int result = h.apply(1); // (1 + 1) * 2 = 4

Function<Integer, Integer> i = f.compose(g); // 먼저 g를 적용한 후 f를 적용
int result2 = i.apply(1); // (1 * 2) + 1 = 3
```

### 3.3. 람다 표현식을 실전에 적용하기: 실행 어라운드 패턴 (Putting Lambdas into Practice: The Execute Around Pattern)

실행 어라운드 패턴은 자원을 획득하고 해제하는 코드가 항상 비슷한 형태를 띨 때 사용되는 디자인 패턴입니다. 이 패턴은 자원 할당과 해제 로직을 템플릿 메서드로 캡슐화하여, 자원 사용 코드를 더욱 간결하고 명확하게 만듭니다. Java에서는 파일 처리나 데이터베이스 연결 등의 작업에서 자주 사용됩니다.

#### 3.3.1. 실행 어라운드 패턴이란?

실행 어라운드 패턴의 핵심은 자원 획득과 해제 코드가 동일한 패턴을 따르며, 중간에 실행할 코드를 람다 표현식으로 전달하는 것입니다. 예를 들어, 파일을 열고 데이터를 읽은 후 파일을 닫는 작업을 생각해볼 수 있습니다.

#### 3.3.2. 기존 방식: 자원 할당 및 해제

먼저, Java 8 이전의 방식으로 파일을 읽고 닫는 예제를 살펴보겠습니다.

```java
public String processFile() throws IOException {
    try (BufferedReader br = new BufferedReader(new FileReader("data.txt"))) {
        return br.readLine();
    }
}
```

위 코드는 `BufferedReader`를 사용하여 파일을 읽고, `try-with-resources` 구문을 사용하여 파일을 닫습니다. 하지만, 이 방식은 파일을 읽는 다양한 로직을 적용하기 어렵습니다.

#### 3.3.3. 람다 표현식을 사용한 실행 어라운드 패턴

이제 실행 어라운드 패턴을 사용하여 파일을 읽는 다양한 로직을 람다 표현식으로 전달할 수 있도록 해보겠습니다.

1. 파일 처리 인터페이스 정의:

```java
@FunctionalInterface
public interface BufferedReaderProcessor {
    String process(BufferedReader br) throws IOException;
}
```

2. 템플릿 메서드 정의:

```java
public String processFile(BufferedReaderProcessor p) throws IOException {
    try (BufferedReader br = new BufferedReader(new FileReader("data.txt"))) {
        return p.process(br);
    }
}
```

3. 람다 표현식을 사용하여 파일 처리 로직 전달:

```java
// 한 줄 읽기
String oneLine = processFile(BufferedReader::readLine);

// 두 줄 읽기
String twoLines = processFile(br -> br.readLine() + br.readLine());
```

이 방식으로 파일을 읽는 다양한 로직을 람다 표현식으로 간단하게 전달할 수 있습니다. 자원 할당과 해제는 `processFile` 메서드에서 처리하므로, 코드 중복을 줄이고 가독성을 높일 수 있습니다.

#### 3.3.4. 예제: 자원 할당 및 해제 패턴

또 다른 예제로, 데이터베이스 연결을 관리하는 코드에서 실행 어라운드 패턴을 적용할 수 있습니다.

1. 데이터베이스 연결 인터페이스 정의:

```java
@FunctionalInterface
public interface ConnectionProcessor<T> {
    T process(Connection conn) throws SQLException;
}
```

2. 템플릿 메서드 정의:

```java
public <T> T executeWithConnection(ConnectionProcessor<T> processor) throws SQLException {
    try (Connection conn = DriverManager.getConnection("jdbc:mysql://localhost/test", "user", "password")) {
        return processor.process(conn);
    }
}
```

3. 람다 표현식을 사용하여 데이터베이스 작업 전달:

```java
// 데이터베이스 쿼리 실행
String result = executeWithConnection(conn -> {
    try (Statement stmt = conn.createStatement();
         ResultSet rs = stmt.executeQuery("SELECT * FROM users")) {
        if (rs.next()) {
            return rs.getString("username");
        }
        return null;
    }
});
```

이 방식으로 데이터베이스 작업을 람다 표현식으로 전달할 수 있으며, 연결 관리 로직을 중앙에서 처리할 수 있습니다.

### 3.4. 함수형 인터페이스 사용하기 (Using Functional Interfaces)

람다 표현식은 함수형 인터페이스와 함께 사용됩니다. 함수형 인터페이스는 단 하나의 추상 메서드를 가지는 인터페이스입니다. Java 8에서는 이미 많은 함수형 인터페이스가 제공되고 있으며, 이를 통해 람다 표현식을 더욱 쉽게 활용할 수 있습니다.

#### 3.4.1. 함수형 인터페이스 정의

함수형 인터페이스는 단 하나의 추상 메서드를 가지는 인터페이스입니다. Java 8에서는 `@FunctionalInterface` 어노테이션을 사용하여 함수형 인터페이스임을 명시할 수 있습니다. 이는 컴파일러가 함수형 인터페이스를 올바르게 구현했는지 확인하도록 도와줍니다.

```java
@FunctionalInterface
public interface MyFunctionalInterface {
    void execute();
}
```

#### 3.4.2. 기본 제공 함수형 인터페이스

Java 8은 여러 가지 기본 제공 함수형 인터페이스를 포함하고 있습니다. 이러한 인터페이스는 `java.util.function` 패키지에 위치해 있으며, 대표적인 인터페이스는 다음과 같습니다:

- `Predicate<T>`: 입력된 인수를 테스트하여 boolean 값을 반환합니다.
- `Function<T, R>`: 입력된 인수를 출력 인수로 매핑합니다.
- `Supplier<T>`: 결과를 제공합니다.
- `Consumer<T>`: 입력된 인수를 받아서 처리합니다.
- `BiFunction<T, U, R>`: 두 개의 입력 인수를 출력 인수로 매핑합니다.

각 인터페이스의 주요 메서드와 예제를 살펴보겠습니다.

##### 3.4.2.1. Predicate<T>

`Predicate<T>`는 입력된 값을 테스트하여 boolean 값을 반환합니다. 보통 조건 검사를 위해 사용됩니다.

예제: 사과의 색상이 녹색인지 확인

```java
Predicate<Apple> isGreenApple = apple -> "green".equals(apple.getColor());

List<Apple> greenApples = filterApples(inventory, isGreenApple);
```

##### 3.4.2.2. Function<T, R>

`Function<T, R>`는 입력 값을 출력 값으로 매핑합니다.

예제: 문자열의 길이를 계산

```java
Function<String, Integer> stringLength = String::length;

Integer length = stringLength.apply("Hello");
```

##### 3.4.2.3. Supplier<T>

`Supplier<T>`는 인수를 받지 않고 값을 반환합니다. 주로 객체 생성에 사용됩니다.

예제: 랜덤 숫자 생성

```java
Supplier<Double> randomValue = Math::random;

Double value = randomValue.get();
```

##### 3.4.2.4. Consumer<T>

`Consumer<T>`는 입력 값을 받아서 특정 작업을 수행합니다.

예제: 문자열을 출력

```java
Consumer<String> print = System.out::println;

print.accept("Hello, World!");
```

##### 3.4.2.5. BiFunction<T, U, R>

`BiFunction<T, U, R>`는 두 개의 입력 값을 받아서 출력 값으로 매핑합니다.

예제: 두 숫자의 합을 계산

```java
BiFunction<Integer, Integer, Integer> add = (a, b) -> a + b;

Integer sum = add.apply(3, 4);
```

#### 3.4.3. 사용자 정의 함수형 인터페이스

기본 제공 함수형 인터페이스 외에도 필요에 따라 사용자 정의 함수형 인터페이스를 만들 수 있습니다. 다음은 두 개의 문자열을 결합하는 사용자 정의 함수형 인터페이스의 예입니다.

```java
@FunctionalInterface
public interface StringCombiner {
    String combine(String s1, String s2);
}

StringCombiner combiner = (s1, s2) -> s1 + " " + s2;

String result = combiner.combine("Hello", "World");
System.out.println(result); // 출력: "Hello World"
```
