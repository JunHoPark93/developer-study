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

// static 에 관한 고찰

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
