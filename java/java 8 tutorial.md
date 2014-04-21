# Java 8 Tutorial (KOREAN)

아래의 모든 내용은 [Java 8 Tutorial](http://winterbe.com/posts/2014/03/16/java-8-tutorial/) 의 내용을 바탕으로 한다.  

목표는 빠른 파악의 레벨이므로 원문에 있는 나름 심오한 내용등은 빠질 수 있다. 그건 원문을 보고 따로 학습하길 바란다. 작업은 2014년 4월 9일 12시부터 시작한다.(이때부터 원문을 읽고 있다)

이 사이트를 알려준 장진우님께 감사드린다.

   영어실력이 잼병이라 원문과 함께 참고하며 읽어나가시기 바랍니다. 그리고 오역있으면 알려주세요. 바로 적용해드리겠습니다. 감사합니다. 


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

# Java 8 tutorial 

소개글 생략

## Default Method for Interface
Java 8 은 default 키워드를 이용함으로써 비-추상 메소드 를 인터페이스에 구현가능하게 해준다.
Extension method 라고하며 여기 우리의 첫 예제가 있다.

``` java
interface Formula {
    double calculate(int a);

    default double sqrt(int a) {
        return Math.sqrt(a);
    }
}
```
인터페이스 `Formula` 의 추상 메소드 `calculate` 를 제외하고는 `sqrt` 메소드는 default 가 정의되어있다. 실제 클래스는 구현해야할 추상메소드 `calculate`만 갖는다. 디폴트 메소드인 `sqrt` 는 그냥 바로 쓸 수 있다.

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

formula 는 익명 형태로 구현되어있다. `sqrt(a*100)` 같은 간단한 계산을 위한 코드 6줄이 있다. 다음섹션에서 보겠지만 Java 8 에서 싱글 메소드 오브젝트를 구현하는 근사한 방법이 있다. 

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

스태틱 유틸리티 메소드인 `Collections.sort` 는 정렬을 위해 list 와 그 list 를 정렬할 comparator 를 받아야 한다. 위와 같은 `Collections.sort`의 구현형태는 익명 `Compatator`를 빈번히 만들어야 한다. 


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

java 컴파일러는 파라미터의 타입을 추측하기에 타입생략이 가능하다. 람다 표현식이 이용되는 형태를 좀더 살펴보자.

## Functional Interface
어떻게 람다표현식이 자바 타입 시스템에 맞춰질까? 각각의 람다는 인터페이스에 지정된 `타입`에 해당한다.  functional interface(함수인터페이스) 라고 불리려면 반드시 하나의 `추상메소드`의 선언을 가지고 있어야 한다. 
그 `타입`의 각각의 람다 표현식은 위에서 말한 `추상메소드`와 매치된다.

당신의 함수인터페이스에 default 를 붙이는것은 자유이다. default 를 붙인 메소드는 이후 더이상 추상이 아니다.

우리는 오직 하나의 추상메소드만 담고 있는 긴 형태의 임의의 람다표현식 인터페이스를 사용할 수 있다. 당신의 인터페이스가 필요조건을 충족하기 위해서는 `@FunctionalInterface` 어노테이션을 붙여야 한다.

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

Java 8 은 `::` 키워드를 통해 메소드나 생성자의 참조를 지나치게 할 수 있다. 위의 예제는 정적메소드 참조의 예를 보여준다. 하지만 또한 우리는 오브젝트의 메소드를 참조할 수 있다. 

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

람다 표현식으로 outer scope 의 변수들을 액세스 하는것은 익명 오브젝트와 유사하다. 지역 outer scope 에서도 final 변수들뿐만 아니라 인스턴스 필드들과 정적 변수들 또한 액세스 가능하다. 

### Accessing local variables
우리는 람다표현식의 outer scope 를 통해 final 로컬 변수들을 읽을 수 있다.

``` java
final int num = 1;
Converter<Integer, String> stringConverter =
        (from) -> String.valueOf(from + num);

stringConverter.convert(2);     // 3
```

그러나 익명 오브젝트들에게는 변수 `num` 은 final로 선언될 필요가 없다는점은 다르다.
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
num = 3;    // 역주, 암묵적으로 final 이므로 대입안됨 
```

람다표현식 내부에서 num 에 값을 쓰는것도 역시 금지된다.

### Accessing fields and static variables

대조를 이루는 로컬변수들, 인스턴스 필드와 정적 변수들에 대해 우리는 람다 표현식 안에서 대해 read/write 접근을 가지고 있다. 이런 형태는 익명 오브젝트들때부터 알려져 온것이다.
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

Default method 들은 람다표현식 안에서는 액세스될 수 **없다.** 다음의 코드는 컴파일 되지 않는다.

``` java
Formula formula = (a) -> sqrt( a * 100);
```



## Built-in Functional Interfaces

JDK 1.8 API 는 많은 빌트인 함수 인터페이스를 가지고 있다. 그중에는 과거의 자바버전에서 부터 알려저온 `Comparator`, `Runnable` 같은게 있다. 이런 존재하는 인터페이스들은 `@FunctionalInterface` 어노테이션을 통해 람다 지원이 가능하도록 확장된다.

그러나 Java 8 API 또한 당신의 삶을 좀더 쉽게 만들어줄 새로운 함수 인터페이스로 가득차있다. 
몇몇 새로운 인트페이스들은 [Google Guava](https://code.google.com/p/guava-libraries/) 라이브러리에서 잘 알려진 것들이다. 이 라이브러리가 친숙하더라도 어떻게 저 인터페이스들이 유용한 메소드 확장들에 의해 확장됐는지 주시해야 한다.

### Predicates
Predicates 는 단일 아규먼트를 받는 boolean(값) 함수이다. 이 인터페이스는 복합적 논리단어(and,or,negate)의 구성을 위한 다양한 default 메소드들을 가지고 있다. 

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
Comparators 는 자바의 이전버전때 부터 잘 알려져왔다. Java 8 은 다양한 default methods 들을 인터페이스에 추가했다. 
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

> 2014년 JCO 설명중 일부 
> STEAM API - INTERMEDIATE OPERATOR 
> - Stream 을 받아서 Stream 을 돌려줌 
> 
> STREAM API - TERMINAL OPERATOR
> - Stream 을 받아서 최종 결과 계산 

`java.util.Stream` 은 하나 혹은 그 이상의 연산(operations)이 수행될 수 있는 elements 들의 시퀀스로 represent 된다.

Stream 연산(operation) 들은 intermediate operation 이거나 terminal operation 이다. terminal operations 들이 명확한 타입의 값을 리턴해주는 반면, intermediate operations 은 steam 그 자신을 리턴하므로, 한줄로 멀티 메소드호출을 짤 수 있다.  

Streams 는 소스, 예로 `java.util.Collection` 같은 list 난 sets(맵은 지원하지 않는다)같은 것에 의해 생성된다. Steam 연산은 시퀀셜하게 실행되거나 병렬적으로 실행될 수 있다. 

시퀀셜 스트림이 동작하는 형태를 처음으로 살펴보자. 문자열의 리스트 형태의 예제소스를 생성하자.

``` java
List<String> stringCollection = new ArrayList<>();
stringCollection.add("ddd2");
stringCollection.add("aaa2");
stringCollection.add("bbb1");
stringCollection.add("aaa1");
stringCollection.add("bbb3");
stringCollection.add("ccc");
stringCollection.add("bbb2");
stringCollection.add("ddd1");
```

Java 8 에서는 `Collection.stream()` 나 `Collection.parallelStream()` 를 호출함으로써 쉽게 streams 를 생성할 수 있도록 확장되었다. 다음에 나올 섹션에서는 가장 흔한 stream 연산을 설명한다.

### Filter
Filter 는 스트림의 모든 요소들을 필터링 하는것을 처리한다. 이 기능은 결과에서의 또다른 stream 연산(e.g foreach)을 호출할수 있도록 해주는 intermediate 이다.(역주. 중간자?) ForEach 는 필터링된 스트림의 원소 각각를 처리하게 해준다.

ForEach 는 terminal operation 이다. 이것은 `void` 이므로 또다른 stream operation 을 호출할 수 없다.

``` java
stringCollection
    .stream()
    .filter((s) -> s.startsWith("a"))
    .forEach(System.out::println);

// "aaa2", "aaa1"
```

### Sorted
Sorted 는 stream 의 sorted view 를 리턴해주는 intermediate operation 이다. 만약 임의의 `Comparator` 를 넘겨주지 않는다면 natural order 를 기준으로 elements 들이 정렬된다.

``` java
stringCollection
    .stream()
    .sorted()
    .filter((s) -> s.startsWith("a"))
    .forEach(System.out::println);

// "aaa1", "aaa2"
```

`sorted`는 돌려준 collection 의 순서의 조작이 없는 stream에 대한 sorted view를 생성할 뿐이라는걸 잊지마라 

``` java
System.out.println(stringCollection);
// ddd2, aaa2, bbb1, aaa1, bbb3, ccc, bbb2, ddd1
```

### Map
intermediate operation 인 `map`은 주어진 함수를 통해 각각의 element 를 또다른 오브젝트로 변환한다. 다음에 나오는 예제는 각각의 문자를 대문자로 변환한다. 허나 물론 또다른 타입으로 각각의 오브젝트를 변환하기 위해 `map` 을 사용할 수 도 있다. 생성되는 stream 제네릭 타입은 map 에 넘긴 함수의 제네릭타입에 따른다. 

``` java
stringCollection
    .stream()
    .map(String::toUpperCase)
    .sorted((a, b) -> b.compareTo(a))
    .forEach(System.out::println);

// "DDD2", "DDD1", "CCC", "BBB3", "BBB2", "AAA2", "AAA1"
```

### Match
다양한 매칭 연산들은 정확히 결정된 stream 매치인지를 체크하는데 사용된다. 이런 모든 연산들은 terminal 이고 boolean 결과를 리턴한다.

``` java
boolean anyStartsWithA =
    stringCollection
        .stream()
        .anyMatch((s) -> s.startsWith("a"));

System.out.println(anyStartsWithA);      // true

boolean allStartsWithA =
    stringCollection
        .stream()
        .allMatch((s) -> s.startsWith("a"));

System.out.println(allStartsWithA);      // false

boolean noneStartsWithZ =
    stringCollection
        .stream()
        .noneMatch((s) -> s.startsWith("z"));

System.out.println(noneStartsWithZ);      // true
```

### Count
Count 는 stream의 다수의 element 들을 `long` 으로 돌려주는 terminal operation이다. 
``` java
long startsWithB =
    stringCollection
        .stream()
        .filter((s) -> s.startsWith("b"))
        .count();

System.out.println(startsWithB);    // 3
```

### Reduce
이 terminal operation 은 주어진 함수로 스트림의 elements 를 줄여주는 작업을 수행한다. 결과는 줄어든 값을 보유한 `Optional` 이다. 

``` java
Optional<String> reduced =
    stringCollection
        .stream()
        .sorted()
        .reduce((s1, s2) -> s1 + "#" + s2);

reduced.ifPresent(System.out::println);
// "aaa1#aaa2#bbb1#bbb2#bbb3#ccc#ddd1#ddd2"
```

## Parallel Streams

위에서 언급한 streams 는 시퀀셜하거나 패러럴(병렬)이 될 수 있다. 

시퀀셜 스트림의 연산은 싱글 스레드에서 처리되는 반면 패러럴 스트림에서의 연산은 멀티플 스레드에서 동시에 처리된다.

다음에 나오는 예제는 병렬 streams 를 이용해서 성능을 올리는게 얼마나 쉬운지 보여주고 있다.

우선 유니크한 elements 들의 커다란 리스트를 생성한다.


``` java
int max = 1000000;
List<String> values = new ArrayList<>(max);
for (int i = 0; i < max; i++) {
    UUID uuid = UUID.randomUUID();
    values.add(uuid.toString());
}
```

이제 이 콜렉션의 stream 을 정렬하는데 걸리는 시간을 측정하자.

### Sequential Sort
``` java
long t0 = System.nanoTime();

long count = values.stream().sorted().count();
System.out.println(count);

long t1 = System.nanoTime();

long millis = TimeUnit.NANOSECONDS.toMillis(t1 - t0);
System.out.println(String.format("sequential sort took: %d ms", millis));

// sequential sort took: 899 ms
```

### Parallel Sort
``` java
long t0 = System.nanoTime();

long count = values.parallelStream().sorted().count();
System.out.println(count);

long t1 = System.nanoTime();

long millis = TimeUnit.NANOSECONDS.toMillis(t1 - t0);
System.out.println(String.format("parallel sort took: %d ms", millis));

// parallel sort took: 472 ms
```

두 코드를 봐서는 거의 비슷하나 parallel 정렬은 대략 50% 정도 빠르다. 해야할것은 stream() 을 parallelStream() 으로 바꾸는게 전부이다.

## Map

map은 stream 을 지원하지 않는다고 앞에서 언급했다. 이런일 처리를 위해 map 대신 이제 다양한 새롭고 유용한 methods 들을 제공한다.

``` java
Map<Integer, String> map = new HashMap<>();

for (int i = 0; i < 10; i++) {
    map.putIfAbsent(i, "val" + i);
}

map.forEach((id, val) -> System.out.println
```

`putIfAbsent` 는 null 체크를 추가하는것을 방지해준다. 
`forEach` 는 map 의 각각의 값을 처리해줄 있도록 해준다.

다음의 예는 유용한 함수를 통해 코드가 어떻게 해석되는지 보여준다.

``` java
map.computeIfPresent(3, (num, val) -> val + num);
map.get(3);             // val33

map.computeIfPresent(9, (num, val) -> null);
map.containsKey(9);     // false

map.computeIfAbsent(23, num -> "val" + num);
map.containsKey(23);    // true

map.computeIfAbsent(3, num -> "bam");
map.get(3);             // val33
```

다음은, 주어진 키와 키에 맵핑된 값에 대해서만 삭제하는 방법을 보여준다. 

``` java
map.remove(3, "val3");
map.get(3);             // val33

map.remove(3, "val33");
map.get(3);             // null
```

또 다른 유용한 메소드로

``` java
map.getOrDefault(42, "not found");  // not found
```

map 의 전체 병합은 아주 쉽다.

``` java
map.merge(9, "val9", (value, newValue) -> value.concat(newValue));
map.get(9);             // val9

map.merge(9, "concat", (value, newValue) -> value.concat(newValue));
map.get(9);             // val9concat
```

키가 엔트리에 존재하지 않을경우 키/값 을 map에 넣거나 기존의 값을 바꾸기 위해 머지 함수가 호출될 것이다. 


## Date API

java 8 은 완전히 새로운 date 와 time API 를 `java.time` 패키지에 포함하고 있다.
새로운 Date API 는 [Joda-Time](http://www.joda.org/joda-time/) 라이브러리와 유사하나 같지는 않다. 다음의 예제는 이 새로운 API 의 중요내용을 대부분 포함하고 있다.

### Clock
Clock 는 현재의 날짜와 시간을 조회할수 있게 해준다. Clock 은 timezone 을 인지하고, `System.currentTimeMillis()` 를 사용하는 대신, 현재의 milliseconds 를 가져온다. 타임라인의 순간적인 지점(point) 도 class `Instant` 로 표현된다. Instants 는 기존의 `java.util.Date` 오브젝트를 생성하기 위해 사용될 수 있다. 

``` java
Clock clock = Clock.systemDefaultZone();
long millis = clock.millis();

Instant instant = clock.instant();
Date legacyDate = Date.from(instant);   // legacy java.util.Date
```

### Timezones

Timezones 는 `ZoneId` 로 표현된다. static factory method 로 쉽게 조회될 수 있다. Timezones 는 offset 을 정의하는데 이는 instants(역주 앞에서 얘기했던 Instant) 와 로컬의 날짜와 시간을 서로 컨버팅하기위해 중요하다. 

``` java
System.out.println(ZoneId.getAvailableZoneIds());
// prints all available timezone ids

ZoneId zone1 = ZoneId.of("Europe/Berlin");
ZoneId zone2 = ZoneId.of("Brazil/East");
System.out.println(zone1.getRules());
System.out.println(zone2.getRules());

// ZoneRules[currentStandardOffset=+01:00]
// ZoneRules[currentStandardOffset=-03:00]
```

### LocalTime

LocalTime 은 10pm 이나 17:30:15 처럼 timezone 없이 시간을 표현한다. 다음은 위에 정의된 timezone을 위한 두개의 로컬시간을 만드는 예제이다. 그러면 우리는 두 시간을 비교하고 두시간 간에 시간과 분이 얼마나 다른지 계산한다.

``` java
LocalTime now1 = LocalTime.now(zone1);
LocalTime now2 = LocalTime.now(zone2);

System.out.println(now1.isBefore(now2));  // false

long hoursBetween = ChronoUnit.HOURS.between(now1, now2);
long minutesBetween = ChronoUnit.MINUTES.between(now1, now2);

System.out.println(hoursBetween);       // -3
System.out.println(minutesBetween);     // -239
```

LocalTime 은 시간 문자열 파싱을 포함한, 새로운 인스턴스의 생성을 간략화 하기 위한 다양한 factory method 와 함께 제공된다.

``` java
LocalTime late = LocalTime.of(23, 59, 59);
System.out.println(late);       // 23:59:59

DateTimeFormatter germanFormatter =
    DateTimeFormatter
        .ofLocalizedTime(FormatStyle.SHORT)
        .withLocale(Locale.GERMAN);

LocalTime leetTime = LocalTime.parse("13:37", germanFormatter);
System.out.println(leetTime);   // 13:37
```

### LocalDate

LocalDate 는 2014-03-11 같은 특정 날짜를 표현한다. 이값은 불변이고, 아나로그를 LocalTime 으로의 처리이다. 샘플 예제는 어떻게 새 날짜를 계산하는지 혹은 날짜,월 또는 년도를 분리하는지를 보여준다. 각각의 조작은 새로운 인스턴스를 생성한다는걸 잊지 마라. 

``` java
LocalDate today = LocalDate.now();
LocalDate tomorrow = today.plus(1, ChronoUnit.DAYS);
LocalDate yesterday = tomorrow.minusDays(2);

LocalDate independenceDay = LocalDate.of(2014, Month.JULY, 4);
DayOfWeek dayOfWeek = independenceDay.getDayOfWeek();
System.out.println(dayOfWeek);    // FRIDAY
```

문자에서 부터 LocalDate 를 파싱하는것은 LocalTime 을 파싱하는것처럼 간단하다.

``` java
DateTimeFormatter germanFormatter =
    DateTimeFormatter
        .ofLocalizedDate(FormatStyle.MEDIUM)
        .withLocale(Locale.GERMAN);

LocalDate xmas = LocalDate.parse("24.12.2014", germanFormatter);
System.out.println(xmas);   // 2014-12-24
```

### LocalDateTime

LocalDateTime 는 date-time 을 표현한다. 이것은 위의 섹션에서 봤던 date 와 time 을 하나의 인스턴스 안에 합치는 것이다. `LocalDateTime` 는 불변이고 LocalTime 과 LocalDate 와 유사하게 동작한다. 우리는 date-time 으로 부터 정확한 필드를 검색하기 위한 수단으로 이용할 수 있다.

``` java
LocalDateTime sylvester = LocalDateTime.of(2014, Month.DECEMBER, 31, 23, 59, 59);

DayOfWeek dayOfWeek = sylvester.getDayOfWeek();
System.out.println(dayOfWeek);      // WEDNESDAY

Month month = sylvester.getMonth();
System.out.println(month);          // DECEMBER

long minuteOfDay = sylvester.getLong(ChronoField.MINUTE_OF_DAY);
System.out.println(minuteOfDay);    // 1439
```

timezone 의 추가적인 정보로 instant 로 컨버트 될 수 있다. Instants 는 기존의 `java.util.Date` 타입으로 쉽게 컨버트 된다.  

``` java
Instant instant = sylvester
        .atZone(ZoneId.systemDefault())
        .toInstant();

Date legacyDate = Date.from(instant);
System.out.println(legacyDate);     // Wed Dec 31 23:59:59 CET 2014
```

형식을 갖춘(Formatting)) date-times 는 형식을 갖춘 date 와 time 와 유사하다. 
pre-defined formats 을 사용하는 대신 우리는 custom 패턴들로 부터 formatter 를 생성할 수 있다.

``` java
DateTimeFormatter formatter =
    DateTimeFormatter
        .ofPattern("MMM dd, yyyy - HH:mm");

LocalDateTime parsed = LocalDateTime.parse("Nov 03, 2014 - 07:13", formatter);
String string = formatter.format(parsed);
System.out.println(string);     // Nov 03, 2014 - 07:13
```

`java.text.NumberFormat` 와 달리 새로운 `DateTimeFormatter` 는 스레드 세이프하고 불변이다.

패턴 문법의 자세한 내용은 [여기](http://download.java.net/jdk8/docs/api/java/time/format/DateTimeFormatter.html) 를 읽어봐라.

## Annotations
java 8 의 Annotations 는 반복될 수 있다. 바로 예제로 확인해보자.

우선, 실제 어노테이션들의 배열로 을 가진 래퍼 어노테이션을 정의한다.

``` java
@interface Hints {
    Hint[] value();
}

@Repeatable(Hints.class)
@interface Hint {
    String value();
}
```

java 8 은 @Repeatable 어노테이션을 선언함으로써 같은 타입의 어노테이션들을 동시에 사용 가능하게 해준다. 


변형 1 : 컨테이너 어노테이션의 이용(구식 형태)
``` java
@Hints({@Hint("hint1"), @Hint("hint2")})
class Person {}
```

변형 2: 반복적인 어노테이션의 이용 (새로운 형태)
``` java
@Hint("hint1")
@Hint("hint2")
class Person {}
```

변형2를 사용하면 자바 컴파일러는 암묵적으로 @Hints 어노테이션을 설정한다.
이것은 reflection 을 통해 어노테이션 정보를 읽기 위해 중요하다.

``` java
Hint hint = Person.class.getAnnotation(Hint.class);
System.out.println(hint);                   // null

Hints hints1 = Person.class.getAnnotation(Hints.class);
System.out.println(hints1.value().length);  // 2

Hint[] hints2 = Person.class.getAnnotationsByType(Hint.class);
System.out.println(hints2.length);          // 2
```

우리가 `Person` 클래스에 @Hints 를 선언하지 않아도 `getAnnotation(Hints.class)`를 통해 여전히 읽을 수 있다. 그러나 @Hint 어노테이션 적용된 모든 것에 대해 직접액세스 가능하게 하는 `getAnnotationsByType` 어노테이션이 좀 더 편리하다.

게다가 java 8 에서의 어노테이션 이용은 2개의 새로운 타겟으로 확장됐다.
``` java
@Target({ElementType.TYPE_PARAMETER, ElementType.TYPE_USE})
@interface MyAnnotation {}
```

## That's it

Java 8 에 대한 나의 프로그래밍 가이드는 여기서 끝이다. JDK 8 API의 새로운 클래스들이나 특징들에 대해서 좀더 알고 싶다면 [다음의 글](http://winterbe.com/posts/2014/03/29/jdk8-api-explorer/)을 읽어라.  몇개 언급하자면 `Arrays.parallelSort`, `StampedLock` 그리고  `CompletableFuture` 같은 JDK 8 의 새로운 클래스들이나 숨겨진 보물들일 찾는데 도움을 줄 것이다. 

나는 최근에 [JAVA 8 Nashorn Tutorial](http://winterbe.com/posts/2014/04/05/java8-nashorn-tutorial/) 를 작성했다. Nashorn Javascript Engine 은 JVM 에서 자바스크립트 코드를 네이트브하게 실행 할 수 있게 해준다.

나는 이 가이드가 당신에게 도움이됐고 즐겁게 읽었기를 바란다. 튜토리어 샘플들의 풀 코드는 [GitHub](https://github.com/winterbe/java8-tutorial) 에 있다. [저장소를 포크](https://github.com/winterbe/java8-tutorial/fork) 하고 싶거나 피드백을 주고 싶다면 [트위터](https://twitter.com/benontherun) 를통해서 연락주면 된다.




