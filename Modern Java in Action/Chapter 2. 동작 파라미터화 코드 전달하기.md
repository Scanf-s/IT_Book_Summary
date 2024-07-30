Chapter 2에서는 "행동 매개변수화"에 대해 설명하며, 이는 소프트웨어 개발 패턴으로 요구 사항 변경에 더 유연하게 대처할 수 있게 해줍니다.

### 2.1. 요구 사항 변경에 대처하기 (Coping with Changing Requirements)

코드를 작성할 때 가장 어려운 부분 중 하나는 변경되는 요구 사항에 적응하는 것입니다. 이 절에서는 점진적으로 코드를 개선하여 요구 사항 변경에 더 유연하게 대처할 수 있는 방법을 살펴봅니다. 농장의 인벤토리 관리 애플리케이션을 예로 들어, 리스트에서 녹색 사과를 필터링하는 기능을 구현하는 방법을 단계별로 개선해보겠습니다.

#### 2.1.1. 첫 번째 시도: 녹색 사과 필터링

먼저, 녹색 사과를 필터링하는 간단한 방법을 구현해보겠습니다.

```java
public static List<Apple> filterGreenApples(List<Apple> inventory) {
    List<Apple> result = new ArrayList<>();
    for (Apple apple : inventory) {
        if ("green".equals(apple.getColor())) {
            result.add(apple);
        }
    }
    return result;
}
```

위 코드는 `filterGreenApples` 메서드를 사용하여 녹색 사과만 필터링합니다. 하지만, 농부가 빨간 사과도 필터링하고 싶다고 요구하면 어떻게 할까요? 이를 위해 동일한 코드를 복제하여 `filterRedApples` 메서드를 만들 수 있습니다. 그러나 이는 유지보수성이 떨어지는 방법입니다.

#### 2.1.2. 두 번째 시도: 색상을 매개변수화하기

코드 중복을 줄이고 더 유연하게 만들기 위해 메서드에 색상을 매개변수로 추가할 수 있습니다.

```java
public static List<Apple> filterApplesByColor(List<Apple> inventory, String color) {
    List<Apple> result = new ArrayList<>();
    for (Apple apple : inventory) {
        if (color.equals(apple.getColor())) {
            result.add(apple);
        }
    }
    return result;
}
```

이제 이 메서드를 사용하여 녹색 사과와 빨간 사과를 필터링할 수 있습니다.

```java
List<Apple> greenApples = filterApplesByColor(inventory, "green");
List<Apple> redApples = filterApplesByColor(inventory, "red");
```

하지만 농부가 무게에 따라 사과를 필터링하고 싶다고 하면 어떻게 할까요? 이를 위해 무게를 매개변수로 추가하는 새로운 메서드를 작성할 수 있습니다.

```java
public static List<Apple> filterApplesByWeight(List<Apple> inventory, int weight) {
    List<Apple> result = new ArrayList<>();
    for (Apple apple : inventory) {
        if (apple.getWeight() > weight) {
            result.add(apple);
        }
    }
    return result;
}
```

이제 이 메서드를 사용하여 무거운 사과를 필터링할 수 있습니다.

```java
List<Apple> heavyApples = filterApplesByWeight(inventory, 150);
```

#### 2.1.3. 세 번째 시도: 모든 속성을 매개변수화하기

하지만 이렇게 하면 메서드가 점점 많아지고, 코드 중복도 여전합니다. 이를 해결하기 위해서는 메서드를 하나로 통합하고, 여러 속성을 기준으로 필터링할 수 있도록 해야 합니다.

```java
public static List<Apple> filterApples(List<Apple> inventory, String color, int weight, boolean flag) {
    List<Apple> result = new ArrayList<>();
    for (Apple apple : inventory) {
        if ((flag && apple.getColor().equals(color)) || (!flag && apple.getWeight() > weight)) {
            result.add(apple);
        }
    }
    return result;
}
```

이제 이 메서드를 사용하여 다양한 조건으로 사과를 필터링할 수 있습니다.

```java
List<Apple> greenApples = filterApples(inventory, "green", 0, true);
List<Apple> heavyApples = filterApples(inventory, "", 150, false);
```

하지만 이 방법은 여전히 최선의 방법이 아닙니다. 메서드 호출이 이해하기 어렵고, 요구 사항이 더 복잡해질 경우 확장하기 어렵습니다.

### 2.2. 행동 매개변수화 (Behavior Parameterization)

행동 매개변수화는 소프트웨어 개발에서 자주 사용되는 패턴으로, 코드의 특정 블록을 나중에 실행하도록 다른 메서드에 전달할 수 있도록 하는 것을 의미합니다. 이를 통해 코드의 유연성을 높이고, 변경 사항에 쉽게 대응할 수 있게 됩니다.

#### 2.2.1. 행동 매개변수화의 개념

행동 매개변수화는 메서드가 동작을 파라미터로 받아들이고, 그 동작을 실행하는 메서드를 의미합니다. 예를 들어, 필터링 조건을 메서드의 파라미터로 전달하여 다양한 필터링 조건을 쉽게 적용할 수 있습니다.

이전 절에서 설명한 사과 필터링 예제를 다시 살펴보겠습니다. 사과의 색상이나 무게를 기준으로 필터링하는 여러 메서드를 작성했는데, 이러한 중복을 줄이고 더 유연하게 만들기 위해서는 행동 매개변수화를 사용할 수 있습니다.

#### 2.2.2. 익명 클래스 사용

Java 8 이전에는 익명 클래스를 사용하여 행동 매개변수화를 구현했습니다. 예를 들어, 사과를 필터링하기 위해 `Predicate<Apple>` 인터페이스를 사용하여 조건을 정의할 수 있습니다.

```java
public interface ApplePredicate {
    boolean test(Apple apple);
}
```

이제 다양한 조건을 익명 클래스로 구현할 수 있습니다.

```java
public class Apple {
    private String color;
    private int weight;
    
    // getters and setters
}

public static List<Apple> filterApples(List<Apple> inventory, ApplePredicate p) {
    List<Apple> result = new ArrayList<>();
    for (Apple apple : inventory) {
        if (p.test(apple)) {
            result.add(apple);
        }
    }
    return result;
}

// 사용 예제
List<Apple> greenApples = filterApples(inventory, new ApplePredicate() {
    public boolean test(Apple apple) {
        return "green".equals(apple.getColor());
    }
});

List<Apple> heavyApples = filterApples(inventory, new ApplePredicate() {
    public boolean test(Apple apple) {
        return apple.getWeight() > 150;
    }
});
```

위 예제에서는 `ApplePredicate` 인터페이스를 사용하여 사과 필터링 조건을 정의하고, `filterApples` 메서드가 이를 받아서 필터링을 수행합니다. 이 방식은 유연성을 제공하지만 여전히 코드가 장황하고 가독성이 떨어집니다.

#### 2.2.3. 람다 표현식으로 행동 매개변수화 간소화

Java 8에서는 람다 표현식을 사용하여 행동 매개변수화를 더 간결하게 구현할 수 있습니다. 람다 표현식을 사용하면 익명 클래스를 사용하는 번거로움을 줄이고, 코드를 더 직관적이고 간결하게 작성할 수 있습니다.

```java
// ApplePredicate 인터페이스를 대신하여 Predicate<T> 사용
import java.util.function.Predicate;

public static List<Apple> filterApples(List<Apple> inventory, Predicate<Apple> p) {
    List<Apple> result = new ArrayList<>();
    for (Apple apple : inventory) {
        if (p.test(apple)) {
            result.add(apple);
        }
    }
    return result;
}

// 사용 예제
List<Apple> greenApples = filterApples(inventory, apple -> "green".equals(apple.getColor()));

List<Apple> heavyApples = filterApples(inventory, apple -> apple.getWeight() > 150);
```

람다 표현식을 사용하면 코드를 훨씬 간결하게 작성할 수 있으며, 메서드가 받는 동작을 쉽게 이해할 수 있습니다. 이로 인해 유지보수성이 높아지고, 요구 사항 변경에 더 유연하게 대응할 수 있습니다.

### 2.3. 장황함 문제 해결 (Tackling Verbosity)

람다 표현식을 사용하여 행동 매개변수화를 통해 코드를 간결하게 만드는 방법을 배웠습니다. 이제 코드를 더 간결하고 명확하게 만드는 다양한 방법에 대해 살펴보겠습니다.

#### 2.3.1. 익명 클래스에서 람다로 변환

익명 클래스는 코드를 복잡하게 만들 수 있습니다. Java 8에서는 이러한 익명 클래스를 람다 표현식으로 대체하여 코드를 간단하게 만들 수 있습니다. 예를 들어, 이전에 익명 클래스로 작성된 `Runnable` 인터페이스를 람다 표현식으로 변환해 보겠습니다.

익명 클래스를 사용하는 경우:
```java
Thread t = new Thread(new Runnable() {
    public void run() {
        System.out.println("Hello, world!");
    }
});
t.start();
```

람다 표현식을 사용하는 경우:
```java
Thread t = new Thread(() -> System.out.println("Hello, world!"));
t.start();
```

람다 표현식을 사용하면 코드가 훨씬 간결해지고 가독성이 높아집니다.

#### 2.3.2. 메서드 참조 사용

람다 표현식보다도 더 간결하게 코드를 작성할 수 있는 방법은 메서드 참조를 사용하는 것입니다. 메서드 참조는 이미 정의된 메서드를 람다 표현식 대신 사용할 수 있도록 합니다. 메서드 참조는 `::` 연산자를 사용하여 표현됩니다.

예를 들어, 파일 필터링을 위해 익명 클래스를 사용하는 경우:
```java
File[] hiddenFiles = new File(".").listFiles(new FileFilter() {
    public boolean accept(File file) {
        return file.isHidden();
    }
});
```

이를 람다 표현식으로 변환한 경우:
```java
File[] hiddenFiles = new File(".").listFiles(file -> file.isHidden());
```

이를 메서드 참조로 변환한 경우:
```java
File[] hiddenFiles = new File(".").listFiles(File::isHidden);
```

메서드 참조를 사용하면 코드가 더욱 간결해지고, 메서드의 동작이 명확하게 드러나기 때문에 가독성이 높아집니다.

#### 2.3.3. 실제 예제: Comparator를 사용한 정렬

Java 8 이전에는 컬렉션을 정렬할 때 익명 클래스를 자주 사용했습니다. 예를 들어, 사과 목록을 무게 순으로 정렬하는 코드는 다음과 같습니다.

익명 클래스를 사용하는 경우:
```java
inventory.sort(new Comparator<Apple>() {
    public int compare(Apple a1, Apple a2) {
        return a1.getWeight().compareTo(a2.getWeight());
    }
});
```

이를 람다 표현식으로 변환한 경우:
```java
inventory.sort((Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight()));
```

이를 메서드 참조로 변환한 경우:
```java
inventory.sort(Comparator.comparing(Apple::getWeight));
```

메서드 참조를 사용하면 정렬 조건이 명확하게 드러나고, 코드가 더욱 간결해집니다.

### 2.4. 실전 예제 (Real-World Examples)

이 장에서는 행동 매개변수화의 개념을 실제로 적용하여 유용하게 사용할 수 있는 몇 가지 예제를 살펴봅니다. 이 예제들은 Java 8에서 람다 표현식과 메서드 참조를 사용하는 방법을 명확하게 보여줍니다.

#### 2.4.1. Comparator를 사용한 정렬

사과 목록을 무게 순으로 정렬하는 예제를 다시 살펴보겠습니다. Java 8 이전에는 익명 클래스를 사용하여 정렬 조건을 정의했습니다.

익명 클래스를 사용하는 경우:
```java
inventory.sort(new Comparator<Apple>() {
    public int compare(Apple a1, Apple a2) {
        return a1.getWeight().compareTo(a2.getWeight());
    }
});
```

람다 표현식을 사용하면 더 간결하게 작성할 수 있습니다.
```java
inventory.sort((Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight()));
```

메서드 참조를 사용하면 코드가 더욱 간결해집니다.
```java
inventory.sort(Comparator.comparing(Apple::getWeight));
```

#### 2.4.2. Runnable을 사용한 스레드 실행

Java에서 스레드를 실행하기 위해 Runnable 인터페이스를 자주 사용합니다. Java 8 이전에는 익명 클래스를 사용하여 Runnable을 구현했습니다.

익명 클래스를 사용하는 경우:
```java
Thread t = new Thread(new Runnable() {
    public void run() {
        System.out.println("Hello, world!");
    }
});
t.start();
```

람다 표현식을 사용하면 코드가 간결해집니다.
```java
Thread t = new Thread(() -> System.out.println("Hello, world!"));
t.start();
```

#### 2.4.3. GUI 이벤트 처리

Java GUI 애플리케이션에서 버튼 클릭과 같은 이벤트를 처리하기 위해 종종 익명 클래스를 사용합니다. 예를 들어, 버튼 클릭 이벤트를 처리하는 코드를 살펴보겠습니다.

익명 클래스를 사용하는 경우:
```java
Button button = new Button("Click me");
button.setOnAction(new EventHandler<ActionEvent>() {
    public void handle(ActionEvent event) {
        System.out.println("Button clicked!");
    }
});
```

람다 표현식을 사용하면 코드가 간결해집니다.
```java
Button button = new Button("Click me");
button.setOnAction(event -> System.out.println("Button clicked!"));
```

#### 2.4.4. 파일 필터링

파일 목록에서 특정 조건에 맞는 파일을 필터링하는 예제를 살펴보겠습니다. Java 8 이전에는 익명 클래스를 사용하여 파일 필터링 조건을 정의했습니다.

익명 클래스를 사용하는 경우:
```java
File[] hiddenFiles = new File(".").listFiles(new FileFilter() {
    public boolean accept(File file) {
        return file.isHidden();
    }
});
```

람다 표현식을 사용하면 코드가 간결해집니다.
```java
File[] hiddenFiles = new File(".").listFiles(file -> file.isHidden());
```

메서드 참조를 사용하면 코드가 더욱 간결해집니다.
```java
File[] hiddenFiles = new File(".").listFiles(File::isHidden);
```
