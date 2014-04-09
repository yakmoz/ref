# Java 8 Tutorial

아래의 모든 내용은 [Java 8 Tutorial](http://winterbe.com/posts/2014/03/16/java-8-tutorial/) 의 내용을 바탕으로 한다.  

목표는 빠른 파악의 레벨이므로 원문에 있는 나름 심오한 내용등은 빠질 수 있다. 그건 원문을 보고 따로 학습하길 바란다. 작업은 2014년 4월 9일 12시부터 시작한다.(이때부터 원문을 읽고 있다)

이 사이트를 알려준 장진우님께 감사드린다.

    안그래도 영어가 별루인데 중간중간 졸면서 옮긴게 있어서 ... 아무튼 오역이 있다면 죄송합니다 -ㅅ-;

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

factory 를 직접 구현하는 대신, 생성자 참조를 통해서 모든걸 붙인다.(glue)






