# AssertJ 정리

> AssertJ가 core document를 새로운 github.io로 이전했네요 :)

본 글은 AssertJ 공식 문서를 핵심 챕터를 선정하여 번역하며 정리한 글 입니다.

<http://joel-costigliola.github.io/assertj/assertj-core.html>

## AssertJ란 무엇인가

AssertJ는 많은 assertion(직역: 주장, 행사)을 제공하는 자바 라이브러리이다. 에러 메세지와 테스트 코드의 가독성을 매우 높여주고 각자 좋아하는 IDE에서 쓰기 굉장히 쉽다.

junit에서 제공하는 assertEquals에 비해 훨씬 가독성이 올라간다. junit의 assertEquals의 인자순서는 헷갈릴 가능성이 크다.

```code
assertEquals(expected, actual); 

assertThat(actual).isEqualTo(expected);
```

밑의 코드가 AssertJ의 assertThat이다. 왼쪽에서 오른쪽으로 자연스럽게 읽히는 것을 알 수 있다. 

### AssertJ의 철학

> AssertJ is built on our spare time, although we are doing our best to give quick feedback sometimes we won't be able to, be patient, be nice, be cool, we all have life to live, families to take care of and time consuming hobbies

원문에서 처럼 AssertJ는 사이드 프로젝트로 만들어졌다. 원작자는 기여는 언제든 환영한다고 한다. 하지만 본인의 삶과 가족이 있기 때문에 빠른 피드백을 줄수 없을 수도 있다고는 하지만 본인의 창작물을 공유하고 끊임없이 개선하는 모습이 인상깊다.

## AssertJ Core Quick Start

```code
<dependency>
  <groupId>org.assertj</groupId>
  <artifactId>assertj-core</artifactId>
  <version>3.12.2</version>
  <scope>test</scope>
</dependency>

testCompile("org.assertj:assertj-core:3.12.2")
```

maven과 gradle 의존성 추가 부분이다. 버젼은 최신화가 계속 이루어지고 java 8밑의 버전에 대한 내용은 공식 문서를 참조하자.

## A Simple Example (아주 간단한 예제)

```java
  @Test
  void a_few_simple_assertions() {
    assertThat("The Lord of the Rings").isNotNull()   
                                       .startsWith("The") 
                                       .contains("Lord") 
                                       .endsWith("Rings"); 
  }
```

설명이 필요없을 정도로 직관적이다. 저 "The Lord of the Rings"라는 문자열이 널이 아니고 The로 시작하며 Lord를 포함하고 Rings로 끝난다라고 바로 알 수 있다.

## Assertion description (Assertion 설명)

assertion이 수행될 때 상황을 설명하는 것이 중요할 때가 있다. 특히 boolean assertion의 경우에 그렇다.

as라는 메서드로 지정을 할 수가 있는데 assertion이 수행되기 **전에** 작성해야 한다.

```java
TolkienCharacter frodo = new TolkienCharacter("Frodo", 33, Race.HOBBIT);

// 실패하는 테스트 예시 그리고 중요한 것은 as()를 assertion이전에 호출해야 한다!
assertThat(frodo.getAge()).as("check %s's age", frodo.getName())
                          .isEqualTo(100);
```

그렇다면 에러메세지는 이런 형식으로 나온다.

```code
[check Frodo's age] expected:<100> but was:<33>
```



## Filtering assertions - iterables나 arrays에 적용되는 filtering

특정 filter를 자바 람다식을 이용하여 표현할 수 있는 유용한 기능이다. 예제코드를 직접 작성해 보았다.

```java
 @Test
    void filter_test() {
        List<Human> list = new ArrayList<>();
        Human kim = new Human("Kim", 22);
        Human park = new Human("Park", 25);
        Human lee = new Human("Lee", 22);
        Human amy = new Human("Amy", 25);
        Human jack = new Human("Jack", 22);

        list.add(kim);
        list.add(park);
        list.add(lee);
        list.add(amy);
        list.add(jack);

        assertThat(list).filteredOn(human -> human.getName().contains("a"))
                .containsOnly(park, jack);
    }
```

Human 클래스는 name과 age를 갖는다. assertThat 구문에서 filteredOn을 사용하는데 직관적으로 이해할 수 있다. 이름을 가져와서 a 가 포함되어 있는 객체들만 필터링을 하고 그 객체를 검증한다. 대소문자를 구별하기 때문에 "Park"과 "Jack"이 나오게 된다. 자바의 stream과 사용법이 거의 같다. 

### 객체의 프로퍼티를 검증

```java
 @Test
    void filter_test2() {
        List<Human> list = new ArrayList<>();
        Human kim = new Human("Kim", 22);
        Human park = new Human("Park", 25);
        Human lee = new Human("Lee", 25);
        Human amy = new Human("Amy", 22);
        Human jack = new Human("Jack", 22);

        list.add(kim);
        list.add(park);
        list.add(lee);
        list.add(amy);
        list.add(jack);

        assertThat(list).filteredOn("age", 25).containsOnly(park, lee);
    }
```

값 세팅은 위 코드와 같은데 assertThat 구문에서 클래스의 프로퍼티에 접근하여 값을 검증하고 있다.

그리고 값에 포함되지 않는 경우도 간결하게 검증할 수 있는 함수를 제공한다.

- not
- in
- notIn

```java
assertThat(list).filteredOn("age", notIn(22)).containsOnly(park, lee);
```

이런식으로 age가 22살이 아닌 객체들을 검증할 수 있다. 

### 프로퍼티를 추출하기

테스트를 위해 리스트의 객체들의 이름을 검증한다고 해보면 반복문에서 이름을 꺼내와 또 다른 리스트에 담고 비교하는 불편한 과정을 수반한다. 하지만 extracting()을 사용하면 이것을 아주 간편하게 해결할 수 있다.

```java
 assertThat(list).extracting("name").contains("Kim", "Park", "Lee", "Amy", "Jack");

```

위의 코드에서는 list를 넘겼지만 list에서 어떤 함수를 부르고 걸러진 값들에 대해서 필드를 추출(extracting)하고 검증할 수 있다. contains가 있으니 당연히 doesNotContain도 존재한다.

그리고 하나의 인자를 검증할 때는 아래와 같이 클래스를 명시하여 타입 검증을 강하게 할 수 있다.

```java
assertThat(list).extracting("name", String.class).contains("Kim", "Park", "Lee", "Amy", "Jack");


```

혹은 이런식으로 튜플로도 추출이 가능하다. 여러 필드를 한 번에 검증할 때 유용하겠다.

```java
     assertThat(list).extracting("name", "age")
                .contains(tuple("Kim", 22),
                        tuple("Park", 25),
                        tuple("Lee", 25),
                        tuple("Amy", 22),
                        tuple("Jack",22));

```

## String assertions (문자열 assertions)

간단하게 문자열 검증을 할 수 있다.

```java
@Test
    void 문자열_검증() {
        String expression = "This is a string";
        assertThat(expression).startsWith("This").endsWith("string").contains("a");
    }

```

## Avoiding incorrect usage (잘못된 사용법 피하기)

### as()를 assertion 이전에 부르지 마라

```java
// Bad: assertion 이후의 as는 작동하지 않는다.
assertThat(actual).isEqualTo(expected).as("description");

```

```java
// Good: as를 assertion 이전에 넣는다.
assertThat(actual).as("description").isEqualTo(expected);

```

### comparator를 assertion 이전에 불러라

```java
// Bad: assertion 이후의 comaparator는 작동하지 않는다.
assertThat(actual).isEqualTo(expected).usingComparator(new CustomComparator());

```

```java
// Good: comaparator를 assertion 이전에 넣는다.
assertThat(actual).usingComparator(new CustomComparator()).isEqualTo("a");

```

## BDD 스타일

bdd스타일은 given, when, then으로 이루어진 스타일이다.

```java
@Test
public void exception_assertion_example() {
   // GIVEN some preconditions

   // WHEN
   Throwable thrown = catchThrowable(() -> { throw new Exception("boom!"); });

   // THEN
   assertThat(thrown).isInstanceOf(Exception.class)
                     .hasMessageContaining("boom");
}

```

이런식으로 조금 더 가독성 있게 작성할 수 있다.

## Exception 처리 test

assertThatThrownBy()라는 예외처리를 가독성 있게 테스트할 수 있는 함수가 제공된다.

기존의 예외 처리 테스트는

```java
  // WHEN
   Throwable thrown = catchThrowable(() -> { throw new Exception("boom!"); });

   // THEN
   assertThat(thrown).isInstanceOf(Exception.class)
                     .hasMessageContaining("boom");

```

이런식으로 Throwable을 가지고 검증을 수행했다면, assertThatThrownBy()는 좀 더 나은 가독성으로 작성할 수 있게 해준다.

```java
@Test
public void exception_assertion_example() {
   assertThatThrownBy(() -> { throw new Exception("boom!"); }).isInstanceOf(Exception.class)
                                                              .hasMessageContaining("boom");
}

```

람다식 안에서 그냥 throw new Exception()을 하여 단순하게 바로 예외를 던졌는데 실제로는 저 식안에 예외가 발생하는 코드를 넣으면 되겠다.

### 자주 쓰이는 예외 처리 syntax

AssertJ에서는 자주 발생하는 예외들에 대해서 정의된 함수를 제공한다. 대표적으로 4가지가 있다.

- assertThatNullPointerException
- assertThatIllegalArgumentException
- assertThatIllegalStateException
- assertThatIOException

사용법은 다음과 같다.

```java
@Test
public void exception_assertion_example() {
   assertThatIOException().isThrownBy(() -> { throw new IOException("boom!"); })
                          .withMessage("%s!", "boom")
                          .withMessageContaining("boom")
                          .withNoCause();
}

```

혹은 자주 쓰이는 저 4가지 예외 타입들이 아니라면 밑의 함수를 이용하면 된다.

```java
@Test
public void testException() {
   assertThatExceptionOfType(IOException.class).isThrownBy(() -> { throw new IOException("boom!"); })
                                               .withMessage("%s!", "boom")
                                               .withMessageContaining("boom")
                                               .withNoCause();
}

```

assertThatExceptionOfType안에 예외 클래스를 넣으면 된다.

### 예외를 던지지 않는 경우 처리

```java
assertThatCode(() -> {
  // code that should throw an exception
  ...
}).doesNotThrowAnyException();

```















