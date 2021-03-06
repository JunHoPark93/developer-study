클래스와 인스턴스 그리고 패키지

## 클래스와 인스턴스

클래스와 인스턴스에 관한 일부 내용은

https://www.slideshare.net/plusjune/ss-46109239?qid=0b8c67e2-c463-4e75-8c77-4ea675a74626

를 참고하여 정리하였습니다.

객체: 객관적으로 존재하는 실체

지향: 위주, 주되는 것으로 삼는 것

Object Oriented: 객체 위주

객체지향 키워드5개: 클래스, 오브젝트, 캡슐화, 상속성, 다형성

### 클래스 (추상)

- 인스턴스를 생성하기 위한 틀 (template)이다. 그래서 클래스 자체만으로는 상태를 가지지 않는다.
- 추상 (abstract)이라는 개념을 가지고 있다. 그래서 클래스는 표현 대상의 공통적인 특징에 대한 서술을 한다.

여기서 추상화(abstraction)란?

-> 대상에서 특징만을 뽑아낸 것.

프로시져 추상화 (Procedural abstraction): 함수

데이터 추상화 (Data abstraction): 구조체, 배열, 포인터

### 오브젝트 (실체), 인스턴스

오브젝트(object)는 클래스의 인스턴스(instance)이다. 즉 클래스가 실체로 만들어진 것을 의미한다. 즉 클래스를 통해 실체화되어 생성된다.

### 인스턴스 메서드, 필드

- 인스턴스 메서드: 인스턴스의 상태를 변경하거나 상태 정보를 반환할 때 사용하는 메서드이다. 그렇기 때문에 인스턴스를 생성한 후 사용할 수 있다.
- 인스턴스 필드: 인스턴스의 상태 정보를 가지고 있는 변수이다.

### 클래스 메서드. 필드

- 클래스 메서드: 인스턴스를 생성하지 않은 상태에서 호출이 가능하며 유틸리티 메서드라고도 부른다.
- 클래스 필드: 여러 인스턴스에서 공유하는 정보가 있는 경우 사용한다.

### static

static은 클래스의 혹은 공통적인 이라는 의미를 가지고 있다. 즉 static 변수 (클래스 변수라고도 함)는 인스턴스에 관계 없이 같은 값을 갖는다. 그 이유는 하나의 변수를 모든 인스턴스가 공유하기 때문이다. 그래서 static이 붙은 변수와 함수는 인스턴스를 생성하지 않고 사용할 수 있다.

## Static 에 대한 고찰

* Static : 클래스의, 공통적인

static은 클래스의 혹은 공통적인 이라는 의미를 가지고 있다. 즉 static 변수 (클래스 변수라고도 함)는 인스턴스에 관계 없이 같은 값을 갖는다. 그 이유는 하나의 변수를 모든 인스턴스가 공유하기 때문이다. 그래서 static이 붙은 변수와 함수는 인스턴스를 생성하지 않고 사용할 수 있다.

그럼 어디다가 쓰는 것이 좋을까?

인스턴스 멤버를 사용하지 않는 메서드에 static을 붙이는 방법을 고려하면 된다. 그렇다면 인스턴스를 생성하지 않기 때문에 편리하고 속도도 빠르다. 

적용해보자! 운영중인 서비스의 유틸 함수이다.

```java
@UtilityClass
public class DomainParser {
    private static final Pattern REFERER_REGEX = Pattern.compile("(//)([A-Z0-9a-zㄱ-ㅎㅏ-ㅣ가-힣:+&@#%?=~_|!:,.;-]+)");
    private static final int REFERER_GET = 2;
    private static final List<String> REFERER_CHECK_LIST = Lists.newArrayList("KAKAO", "LINE");

    public static String getDomain(String referer, String userAgent) {
        String domainFromReferer = fromReferer(referer);
        if (Objects.nonNull(domainFromReferer)) {
            return domainFromReferer;
        }

        String domainFromUserAgent = fromUserAgent(userAgent);
        if (Objects.nonNull(domainFromUserAgent)) {
            return domainFromUserAgent;
        }

        return PlatformUtil.checkMobile(userAgent);
    }


    private static String fromReferer(String referer) {
        Matcher matcher = REFERER_REGEX.matcher(referer);
        if (matcher.find()) {
            return matcher.group(REFERER_GET).replaceAll("www\\.", "");
        }

        return null;
    }

    private static String fromUserAgent (String userAgent) {
        String userAgentUpper = userAgent.toUpperCase();
        for (String platForm : REFERER_CHECK_LIST) {
            if (userAgentUpper.contains(platForm)) {
                return platForm;
            }
        }

        return null;
    }
}
```

안에서 동작하는 3가지의 함수는 특별히 멤버변수를 쓰지 않는다. (위의 선언된 패턴과 숫자들은 상수이다) 저 밑의 fromReferer 함수와 fromUserAgent 함수가 지금보니 static으로 선언되지 않았다. 이 부분을 모두 static 함수로 리팩토링을 진행하였다.

#### Final 에 대한 고찰

final은 마지막, 변경 될 수 없는 이라는 뜻을 지니고 있다. 변수에 사용되면 값을 변경할 수 없는 상수가 되며, (위의 예처럼) 메서드에 사용하면 오버라이딩을 할 수 없게 되고 클래스에 사용되면 그 클래스를 상속할 수 없게 된다. 그리고 선언과 동시에 초기화할 수 있고 선언만 해 놓고 생성자에서 초기화하는 것 까지 가능하다. 생성자에서 초기화 하는 것은 각 인스턴스변수가 각기 다른 final 변수를 가지게 하기 위함이다.


##### static과 final 사용시 주의 점

접근 제어자가 private 인 메서드는 오버라이딩 될 수 없으므로 final을 굳이 붙일 필요가 없다. abstract 메서드는 자손쪽에서 구현해야 하므로 private 일 수 없다. static 메서드는 몸통이 있는 메서드에 사용 가능하므로 abstract을 함께 쓸 수 없다. 

(static 참조) 자바의 정석 


#### 참고) util 클래스의 private 생성자

유틸 클래스의 객체생성을 막기 위해서 (static 메서드만 존재할 시) 빈 생성자를 private로 만들어 명시적으로 클래스를 생성할 수 없도록 하는 것이 개발자 입장에서 더 좋다.

### 인스턴스 필드, 클래스 필드의 유효 기간

인스턴스 변수의 유효 기간은 인스턴스가 생성되었다가 소멸되는 시점까지이다.

클래스 변수의 유효 기간은 애플리케이션을 실행하고 JVM이 클래스를 로드(클래스가 필요한 시점에 동적으로 클래스 바이트 코드를 읽어 메모리에 할당하는 과정)하는 시점부터 클래스가 언로드(클래스를 더 이상 사용하지 않는 경우 메모리에서 클래스를 해제하는 과정) 하는 시점 까지 이다.

## 객체지향의 특징

### 캡슐화 

> 캡슐화란 객체의 속성(data fileds)과 행위(methods)를 하나로 묶고, 실제 구현 내용 일부를 외부에 감추어 은닉한다 - 위키

캡슐화는 [데이터 + 데이터에 대한 조작]을 묶는 것이다.

### 상속

하나의 클래스가 가지고 있는 특징들을 그대로 다른 클래스가 물려 받는 것

### 다형성

상속성의 계층을 따라서 각각의 클래스에 한 가지 이름을 줄 수 있는 것

> 다형성은 그 프로그래밍 언어의 자료형 체계의 성질을 나타내는 것으로, 프로그램 언어의 각 요소들이 다양한 자료형에 속하는 것이 허가되는 성질을 가리킨다 - 위키

같은 명령을 각기 다른 오브젝트들에 줄 수 있다는 뜻이다.

```
Dog d;

d = new Poodle();
d.bark(); // 객체 d에 bark라는 메세지를 보낸다라고 읽는다.

d = new Bulldog();
d.bark();
```

코드 출처: <https://www.slideshare.net/plusjune/ss-46109239?qid=0b8c67e2-c463-4e75-8c77-4ea675a74626>

메세지 전달 (message passing)

위의 코드에서처럼 객체지향관점에서 프로그램은 오브젝트에 message를 보내는 것으로 실행한다.

## 객체지향 패러다임

객체지향은 프로그래밍 패러다임 중의 하나이다.

- 구조적 프로그래밍: 파스칼, C
- 객체지향 프로그래밍: 자바, C++
- 명령형 프로그래밍: 포트란, 코볼, 알골, 파스칼, C, 에이다
- 선언형 프로그래밍: 프롤로그, SQL
- 함수형 프로그래밍: 하스켈, 리스프

### 객체지향의 장점

- 자연스런 모델링
- 생산성 향상
- 점진적 개발
- 견고한 의미적 모델링
- 명확한 인터페이스 기술
- 보안
- 유지보수의 용이성

## 패키지(package)

패키지란 자바 클래스들을 모아놓는 디렉터리이다. 

장점:

- 많은 클래스들을 찾는 수고를 덜어준다. 
- 클래스의 분류가 용이하다.
- 패키지가 다르다면 동일한 클래스명을 사용할 수 있다.
- 소스 코드 충돌을 방지한다.

컨벤션:

- com.company-name.program-name 혹은 com.company-name.platform-name.program-name
- 모든 패키지 이름은 소문자를 사용한다.
- 패키지 이름을 java, javax로 시작해서는 안된다. 이 두개는 jdk에서 독점적으로 사용하는 패키지 이름이기 때문이다.

기본패키지:

자바는 기본이 되는 패키지를 자동으로 import해주기 때문에 String 과 같은 클래스를 import 없이 사용할 수 있다.

## 인터페이스

> <참조: 자바의 정석>

인터페이스란 일종의 추상 클래스이다. 하지만 추상 클래스보다 추상화 정도가 높아서 추상클래스와 달리 몸통을 갖춘 일반 메서드 또는 멤버변수를 가질 수 없다. 추상 클래스를 미완성 설계도라고 한다면 인터페이스는 기본 설계도라고 할 수 있다. 

특징으로는 모든 멤버변수는 public static final 이 기본이며 (생략 가능) 모든 메서드는 public abstract가 기본이다. 


### 인터페이스의 장점

1. 개발 시간을 단축

인터페이스가 작성되면 메서들르 호출하는 쪽에서는 메서드의 내용에 관계없이 선언부만 알면 된다. 그래서 양쪽에서 동시에 개발이 가능하다.

2. 표준화

프로젝트에 사용되는 기본 틀을 인터페이스로 작성한 다음, 개발자들에게 인터페이스를 구현하여 프로그램을 작성하도록 하여 일관되고 정형화된 프로그램이 가능하다.

3. 클래스간의 관계

서로 상속관계나 같은 조상 클래스를 가지고 있지 않은 클래스들에게 하나의 인터페이스를 공통 구현시킴으로써 관계를 맺어줄 수 있다.

4. 독립적인 프로그래밍이 가능

인터페이스를 이용하면 선언과 구현을 분리시킬 수 있기 때문에 실제 구현에 독립적인 프로그래밍이 가능하다.

책에서는 스타크래프트의 예시를 들어서 클래스간의 관계를 맺어주는 예시를 들었다.

GroundUnit: Marine, SCV, Tank

AirUnit: Dropship

지상유닛과 공중유닛은 나뉘어져 있다. 하지만 수리(repair)라는 행위를 하기 위해서는 수리공에게 repair메서드를 정의해주어야 한다.

```java
void repair(Tank t) {
    // 탱크 수리
}
void repiar(Dropship d) {
    // 드랍쉽 수리
}
```

여기서 현재 유닛들이 구현하고 있는 인터페이스로 변경한다면,

```java
void repiar(GroundUnit gu) {
    // 지상 유닛 수리
}
void repair(AirUnit au) {
	// 공중 유닛 수리   
}
```

이렇게 변경할 수 있는데 이것도 두 개의 메서드가 필요하다.

해결책은 각 유닛마다 Repairable이라는 인터페이스를 구현하게 하는 것이다.

```java
class Tank extends GroundUnit implements Repairable {
    // ...
}
class Dropship extends AirUnit implements Repairable {
    // ...
}
```

그리고 나서 repair 메서드를 호출할 때는 매개변수를 저 인터페이스의 타입으로 해주면 하나의 메서드로 구현 가능하다.

```java
void repair(Repairable r) {
    // 수리
}
```

이렇게 같은 인터페이스를 구현함으로써 관계를 맺어줄 수 있다. 

## Stream

스트림은 데이터의 흐름이다. 람다를 이용해서 코드의 양을 줄이고 간결하게 표현할 수 있다. 그리고 병렬처리가 가능하다. 쓰레드를 이용해 많은 요소들을 빠르게 처리할 수 있다. 

기존 방법은 for문과 iterator를 사용했던 것인데 너무 길고 알아보기 어렵다. 또한 List를 정렬할 때는 Collections.sort()를 사용하고 배열을 정렬할 때에는 Arrays.sort()를 사용해야 하기 때문에 불편하다.

스트림은 데이터 소스를 추상화 하였고, 데이터를 다루는데 자주 사용되는 메서드들을 정의해 놓았다. 데이터 소스를 추상화 한것은 데이터 소스가 무엇이든 간에 같은 방식으로 다룰 수 있게 되었다는 것과 코드의 재 사용성이 높아진 것을 의미한다. 

### 스트림은 데이터 소스를 변경하지 않는다.

스트림은 데이터 소스로부터 데이터를 읽기만 할 뿐, 데이터 소스를 변경하지 않는다. 

### 스트림은 일회용이다.

스트림은 iterator 처럼 일회용이다. 스트림도 한번 사용하면 닫혀서 다시 사용할 수 없다. 

### 스트림은 작업을 내부 반복으로 처리한다.

스트림을 이요한 작업이 간결할 수 있는 이유는 내부반복에 있다. 즉 반복문을 메서드의 내부에 숨기는 것이다. 

### 스트림의 연산

스트림은 중간 연산과 최종 연산이 있다. 중간 연산은 연산결과를 스트림으로 반환하기 때문에 체이닝을 할 수 있다. 최종 연산은 스트림의 요소를 소모하면서 일어나기 때문에 한 번만 가능하다.





