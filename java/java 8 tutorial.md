# Java 8 Tutorial (KOREAN)

아래의 모든 내용은 [Java 8 Tutorial](http://winterbe.com/posts/2014/03/16/java-8-tutorial/) 의 내용을 바탕으로 한다.  

목표는 빠른 파악의 레벨이므로 원문에 있는 나름 심오한 내용등은 빠질 수 있다. 그건 원문을 보고 따로 학습하길 바란다. 작업은 2014년 4월 9일 12시부터 시작한다.(이때부터 원문을 읽고 있다)

이 사이트를 알려준 장진우님께 감사드린다.

    안그래도 영어가 별루인데 중간중간 졸면서 옮긴게 있어서 ... 아무튼 오역이 있다면 죄송합니다 -ㅅ-;
    그리고 아무래도 잘 모르는 내용들이다보니 번역하기가 어려워서 원문을 적어놓느것도 있습니다.
    흐음... 지금 당장봐도 오역이 있는것 같군요. 휴... 시간이 걸리겠지만 업데이트해보도록 하겠습니다.
    완성도가 올라가기 전까지는 참고용으로만 쓰시기 바랍니다. 

## 번역참고사항

- anonymous : 익명 
- lambda : 람다
- expressions : 표현식
- lambda expressions : 람다표현식 
- syntax : 문법  
- functional interface : 함수 인터페이스 
- annotation : 어노테이션 
- simplified : 간략화 
- static method : 정적 메소드 
- References : 참조 
- method : 메소드 
- Constructor References : 생성자 참조 
- static variables : 정적 변수
- instance fields : 인스턴스 필드들 
- built-in : 빌트인 

## Default Method for Interface
아주 간단히 말하면 인터페이스에 구현이 가능하다.
``` java
interface Formula {
    double calculate(int a);

    default double sqrt(int a) {
        return Math.sqrt(a);
    }
}
```

위에 `default` 라는 키워드가 있는데 이 키워드를 적용하면 비추상 메소드 구현이 가능해진다. Extension method 라고도 한단다.

아래는 구현이다.
``` java
Formula formula = new Formula() {
    @Override
    public double calculate(int a) {
        return sqrt(a * 100);
    }
};

formula.calculate(100);     // 100.0
formula.sqrt(16);           // 4.0
```

실제 이 인터페이스를 이용할때는 default 를 적용한 sqrt 를 구현하지 않아도 된다. 

## Lambda expressions

아래는 이전버전에서의 자바 문장 sort 예제이다. 

``` java
List<String> names = Arrays.asList("peter", "anna", "mike", "xenia");

Collections.sort(names, new Comparator<String>() {
    @Override
    public int compare(String a, String b) {
        return b.compareTo(a);
    }
});
```
위와 같은 `Collections.sort`의 구현형태는 익명 `Compatator`를 빈번히 만들어야 한다. 

이런 익명의 오브젝트를 만드는것 대신 Java 8 은 람다표현식 같은 짧은 문법 이 제공한다. 

``` java
Collections.sort(names, (String a, String b) -> {
    return b.compareTo(a);
});
```

더 짧게도 가능하다.

``` java
Collections.sort(names, (String a, String b) -> b.compareTo(a));
```

한줄 메소드 형태는 {}, return 키워드를 뺄 수 있다. 이게 더 짧게도 가능하다.

``` java
Collections.sort(names, (a, b) -> b.compareTo(a));
```

java 컴파일러는 파라미터의 타입을 추측하기에 String 을 생략가능하다. 

## Functional Interface
어떻게 람다표현식이 자바 타입 시스템에 맞춰질까? 각각의 람다는 인터페이스에 지정된 `타입`에 해당한다.  functional interface(함수인터페이스) 라고 불리려면 반드시 하나의 `추상메소드`의 선언을 가지고 있어야 한다. 
그 `타입`의 각 람다 표현식은 위에서 말한 `추상메소드`와 매치된다.

당신의 함수인터페이스에 default 를 붙이는것은 자유이다. default 를 붙인 메소드는 이후 더이상 추상이 아니다.

우리는 오직 하나의 추상메소드만 담고 있는 긴 형태의 임의의 람다표현식 인터페이스를 사용할 수 있다. 당신의 인터페이스가 필요조건을 충족하기 위해서는 @FunctionalInterface 어노테이션을 붙여야 한다.

컴파일러는 이 어노테이션을 인지하며, 인터페이스에 추가 추상메소드를 선언하려고 하면 즉시 컴파일러 에러로 처리한다. 

``` java
@FunctionalInterface
interface Converter<F, T> {
    T convert(F from);
}
```

``` java
Converter<String, Integer> converter = (from) -> Integer.valueOf(from);
Integer converted = converter.convert("123");
System.out.println(converted);    // 123
```

만약 `@FunctionalInterface` 를 뺀다면 이 소스는 valid 하다는걸 기억하라.

## Method and Constructor References

앞의 코드는 정적 메소드 참조를 통해 좀더 간략화 될 수 있다. 

``` java
Converter<String, Integer> converter = Integer::valueOf;
Integer converted = converter.convert("123");
System.out.println(converted);   // 123
```

Java 8 은 `::` 키워드를 통해 메소드나 생성자 참조를 지나치게 할 수 있다. 위의 예제는 정적메소드 참조의 예를 보여준다. 하지만 또한 우리는 오브젝트의 메소드를 참조할 수 있다. 

``` java
class Something {
    String startsWith(String s) {
        return String.valueOf(s.charAt(0));
    }
}
```

``` java
Something something = new Something();
Converter<String, String> converter = something::startsWith;
String converted = converter.convert("Java");
System.out.println(converted);    // "J" 
```

`::` 키워드가 어떻게 생성자를 대리하는지 살펴보자. 우선 여러 다른 생성자들을 가진 빈을 정의하자.

``` java
class Person {
    String firstName;
    String lastName;

    Person() {}

    Person(String firstName, String lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    }
}
```
다음, 새로운 사람들을 생성하기 위한 person factory 인터페이스를 정의 한다. 

``` java
interface PersonFactory<P extends Person> {
    P create(String firstName, String lastName);
}
```

factory 를 직접 구현하는 대신, 생성자 참조를 통해 모든걸 조립한다.(glue)

``` java
PersonFactory<Person> personFactory = Person::new;
Person person = personFactory.create("Peter", "Parker");
```

우리는 Person::new 로 Person 생성자의 참조를 생성했다. 자바 컴파일러는 `PersonFactory.create` 의 시그니처 매칭을 통해 올바른 생성자를 자동적으로 고른다. 

## Lambda Scopes 

람다 표현식으로 outer scope 의 변수들을 액세스 하는것은 익명 오브젝트에게 하는것과도 유사하다. 지역 outer scope 에서도 final 변수들뿐만 아니라 인스턴스 필드들과 정적 변수들 또한 액세스 가능하다. 

### Accessing local variables
우리는 람다표현식의 outer scope 를 통해 final 로컬 변수들을 읽을 수 있다.

``` java
final int num = 1;
Converter<Integer, String> stringConverter =
        (from) -> String.valueOf(from + num);

stringConverter.convert(2);     // 3
```

그러나 익명 오브젝트들에게는 변수 `num` 은 `final` 로 선언될 필요가 없다는점은 다르다.
이 코드또한 valid 하다.

``` java
int num = 1;
Converter<Integer, String> stringConverter =
        (from) -> String.valueOf(from + num);

stringConverter.convert(2);     // 3
```

그러나 `num` 은 반드시 코드가 컴파일 되기 위해 암묵적으로 fianl 이어야 한다. 다음의 코드는 컴파일 되지 않는다.

``` java
int num = 1;
Converter<Integer, String> stringConverter =
        (from) -> String.valueOf(from + num);
num = 3;
```

람다표현식 내부에서 num 에 값을 쓰는것도 역시 금지된다.

### Accessing fields and static variables

대조를 이루는 로컬변수들, 인스턴스 필드와 정적 변수들에 대해 우리는 람다 표현식 안에서 대해 read/write 접근을 가지고 있다. 이런 형태는 익명 오브젝트들때부터 알려져 왔다.
>In constrast to local variables we have both read and write access to instance fields and static variables from within lambda expressions. This behaviour is well known from anonymous objects.(원문)

``` java
class Lambda4 {
    static int outerStaticNum;
    int outerNum;

    void testScopes() {
        Converter<Integer, String> stringConverter1 = (from) -> {
            outerNum = 23;
            return String.valueOf(from);
        };

        Converter<Integer, String> stringConverter2 = (from) -> {
            outerStaticNum = 72;
            return String.valueOf(from);
        };
    }
}
```

### Accessing Default Interface Methods
첫번째 섹션의 formula 예제를 기억하는가? 인터페이스 `Formula` 는 각각의 formula 인스턴스들이나 익명의 오브젝트들에서도 액세스 가능한 default method 인  `sqrt` 를 정의 했다. 이것은 람다 표현식과는 동작하지 않는다. 

Default method 들은 람다표현식 안에서는 액세스될 수 없다. 다음의 코드는 컴파일 되지 않는다.

``` java
Formula formula = (a) -> sqrt( a * 100);
```



## Built-in Functional Interfaces

JDK 1.8 API 는 많은 빌트인 함수 인터페이스를 가지고 있다. 그중에는 과거의 자바버전에서 부터 알려저온 `Comparator`, `Runnable` 같은게 있다. 이런 존재하는 인터페이스들은 `@FunctionalInterface` 어노테이션을 통해 람다 지원이 가능하도록 확장된다.

그러나 Java 1.8 API 또한 당신의 삶을 좀더 쉽게 만들어줄 새로운 함수 인터페이스로 가득차있다. 
몇몇 새로운 인트페이스들은 [Google Guava](https://code.google.com/p/guava-libraries/) 라이브러리에서 잘 알려진 것들이다. 이 라이브러리가 친숙하더라도 어떻게 저 인터페이스들이 유용한 메소드 확장들에 의해 확장됐는지 주시해야 한다.

### Predicates
Predicates 는 단일 아규먼트를 받는 boolean(값) 함수이다. 이 인터페이스는 복합적 논리단어(and,or,negate)의 구성을 위한 다양한 default 메소드들 가지고 있다. 

``` java
Predicate<String> predicate = (s) -> s.length() > 0;

predicate.test("foo");              // true
predicate.negate().test("foo");     // false

Predicate<Boolean> nonNull = Objects::nonNull;
Predicate<Boolean> isNull = Objects::isNull;

Predicate<String> isEmpty = String::isEmpty;
Predicate<String> isNotEmpty = isEmpty.negate();
```

### Functions
Functions 는 단일 아규먼트를 받아 결과를 만들어낸다. Default 메소드들은 다수의 함수들과 함께 엮기 위해 사용될 수 있다. (compose, andThen)

``` java
Function<String, Integer> toInteger = Integer::valueOf;
Function<String, String> backToString = toInteger.andThen(String::valueOf);

backToString.apply("123");     // "123"
```

### Suppliers
Suppliers 는 주어진 제네릭타입의 결과를 생성한다. 함수들과 달리, Suppliers 는 아규먼트를 받지 않는다. 
``` java
Supplier<Person> personSupplier = Person::new;
personSupplier.get();   // new Person
```

### Consumers
Consumers 는 단일 입력 아규먼트를 받아 실행될 동작들을 나타낸다.

``` java
Consumer<Person> greeter = (p) -> System.out.println("Hello, " + p.firstName);
greeter.accept(new Person("Luke", "Skywalker"));
```

### Comparators
Comparators 는 자바의 이전버전때 부터 잘 알려져왔다. Java 8 은 당얀한 default methods 들을 인터페이스에 추가했다. 
``` java
Comparator<Person> comparator = (p1, p2) -> p1.firstName.compareTo(p2.firstName);

Person p1 = new Person("John", "Doe");
Person p2 = new Person("Alice", "Wonderland");

comparator.compare(p1, p2);             // > 0
comparator.reversed().compare(p1, p2);  // < 0
```

### Optionals
Optionals 은 함수인터페이스가 아니고 `NullPointerException` 을 막는 멋진 유틸리티이다.
이것은 다음섹션을 위해서 중요한 컨셉인데, Optionals이 어떻게 동작하는지 간단히 살펴보자.

Optional 은 null 이나 non-null 을 수 있는 값을 위한 간단한 컨테이너이다. 보통은 non-null 을 리턴하지만 가끔은 아무것도 리턴하지 않는 메소드를 생각해보자. Java 8 에서는 null 을 리턴하는대신 `Optional` 을 리턴할 수 있다.

``` java
Optional<String> optional = Optional.of("bam");

optional.isPresent();           // true
optional.get();                 // "bam"
optional.orElse("fallback");    // "bam"

optional.ifPresent((s) -> System.out.println(s.charAt(0)));     // "b"
```

## Streams
> represent 는 일종의 명령 집합, 실행될 일들 을 의미한다고 보면된다.

> 다음은 intermediate 와 terminal 에 대한 설명이다. [[오라클 홈페이지]](http://docs.oracle.com/javase/tutorial/collections/streams/) 
> 
> Zero or more intermediate operations. An intermediate operation, such as filter, produces a new stream.
> 
> A stream is a sequence of elements. Unlike a collection, it is not a data structure that stores elements. Instead, a stream carries values from a source through a pipeline. This example creates a stream from the collection roster by invoking the method stream.
> 
> The filter operation returns a new stream that contains elements that match its predicate (this operation's parameter). In this example, the predicate is the lambda expression e -> e.getGender() == Person.Sex.MALE. It returns the boolean value true if the gender field of object e has the value Person.Sex.MALE. Consequently, the filter operation in this example returns a stream that contains all male members in the collection roster.  
> 
>   A terminal operation. A terminal operation, such as forEach, produces a non-stream result, such as a primitive value (like a double value), a collection, or in the case of forEach, no value at all. In this example, the parameter of the forEach operation is the lambda expression e -> System.out.println(e.getName()), which invokes the method getName on the object e. (The Java runtime and compiler infer that the type of the object e is Person.)  

`java.util.Stream` 은 엘러먼트들의 시퀀스, 하나 혹은 그 이상의 오프레이션들이 수행될 수 있는 것들을 represent 한다.
Stream operation 들은 intermediate operation 이거나 terminal operation 이다. 








