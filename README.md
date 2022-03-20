# DDD
Domain Driven Design


# chap1
+ [도메인 모델](https://unique-wandflower-4cc.notion.site/984e2febd52a43eca9533d889ebb3f8f)
+ [엔티티와 밸류](https://unique-wandflower-4cc.notion.site/360fa859372c49a296a0b6d6449a64ef)

## chap1 정리
# chap1. 도메인_모델_시작 정리

### 도메인

`도메인` - 소프트웨어로 해결하고자 하는 문제 영역

### 도메인 모델

특정 도메인을 개념적 모델로 표현한 것

### 도메인 모델 패턴

도메인 계층을 객체지향 기법으로 구현한 패턴

도메인 아키텍쳐는 표현 - 응용 - 도메인 - 인프라스트럭쳐 로 구성되 있다

`개념 모델` - 문제를 분석한 순수한 모델

`구현 모델` - 개념 모델을 구현 가능한 형태의 모델로 변환하는 과정

### 도메인 모델 추출

도메인 모델링할 때 기본이 되는 작업은 모델을 구성하는 핵심 구성요소  , 규칙 , 기능을 찾는 것이다

요구사항을 바탕으로 어떤 기능과 데이터를 가져야 할 지에 초점을 맞춰 도메인 모델을 점진적으로 만들어 갈 수 있다

### 엔티티와 밸류

`엔티티` - 식별자를 갖는다 , 식별자는 엔티티 객체마다 고유하다

`벨류` - 개념적으로 완전한 하나를 표현할 때 사용한다

- 밸류 타입 적용전

```java
public class ShippingInfo {
    private String receiverName;
    private String receiverPhoneNumber;
    
    private String shippingAddress1;
    private String shippingAddress2;
    private String shippingZipcode;
}
```

- 밸류 타입 적용후

```java
public class Receiver {
    private String name;
    private String phoneNumber;
    
    public Receiver(String name , String phoneNumber) {
        this.name = name;
        this.phoneNumber = phoneNumber;
    }
    
    public String getName() {
        return name;
    }
    
    public String getPhoneNumber() {
        return phoneNumber;
    }
}
```

```java
public class Address {
    private String address1;
    private String address2;
    private String zipcode;
}
```

### 도메인 용어

```java
public enum OrderState {
    PAYMENT_WAITING, PREPARING, SHIPPED, DELIVERING< DELIVERY_COMPLETED;
}
```

- 도메인에서 사용하는 용어를 최대한 코드에 반영하면 코드를 도메인 용어로 해석하거나 도메인 용어를 코드로 해석하는 과정이 줄어든다
- 이는 코드의 가독성을 높여 코드를 분석하고 이해하는 시간을 절약한다
- 또한 도메인 용어를 사용해서 최대한 도멩니 규칙을 코드로 작성하게 되므로 의미를 변환하는 과정에서 발생하는 버그도 줄어들게 된다


# chap2
## 아키텍처 개요
### 네개의 영역
+ 표현
+ 응용
+ 도메인 
+ 인프라스트럭처

### 계층 구조 아키텍처
+ 인프라스트럭처에 의존하면 테스트하기가 어렵다
+ 또한 기능확장의 어려움이 있다
+ 이를 해결하기 위해 DIP를 적용해야 한다


### DIP
+ 저수준 모듈이 고수준 모듈에 의존한다
+ 자신보다 변하기 쉬운것에 의존하지 않는다
+ 자신보다 변하기 쉬운 것에 의존하던 것을 추상화된 인터페이스나 상위 클래스를 두어 변하기 쉬운 것의 변화에 영향받지 않게 한다


+ 스노우타이어가 아닌 다른 타이어로 자동차가 갈아껴야 한다면
+ 자동차 클래스의 전체 코드를 변경해야 한다

![dip_적용전](https://user-images.githubusercontent.com/42866800/159007475-1cf0989a-45fc-423d-8e5f-52c22d447581.png)

+ 자동차가 의존하고 있는 타이어가 변경되어도 타이어 인터페이스를 구현한 구현체만 바꿔주면 되기 때문에 자동차 클래스는 건드리지 않아도 된다

![DIP 적용후](https://user-images.githubusercontent.com/42866800/159007516-2aad0b88-199b-4b20-8aed-fc89379ff781.png)

### 블로그 프로젝트에 DIP를 적용할 수 있는 방법
+ DAO용 mapper 인터페이스와 mapper.xml 파일에 DIP를 적용한다
+ DAO는 도메인 패키키 하위에 만들고 mapper.xml 파일의 경우 resources 폴더 밑에 인프라스트럭처 디렉토리를 만들어 파일을 생성한다
+ 서비스가 DAO에 의존하면 안된다
+ 따라서 mapper 인터페이스 구현체를 만들어 mapper 인터페이스를 상속받고 서비스단에서는 mapper 인터페이스를 DI하여 사용한다
+ 다른 인터페이스 구현체가 추가되거나 인터페이스 구현체가 변경되는 경우 서비스단 코드는 변경하지 않아도 된다

### 도메인 영역의 주요 구성 요소

#### 엔티티와 밸류
+ 도메인 모델의 엔티티는 데이터와 함께 기능을 제공하는 객체이다
+ 도메인 관점에서 기능을 구현하고 캡슐화 하여 데이터가 임의로 변경되는 것을 막는다

+ 도메인 모델의 엔티티는 두 개 이상의 데이터가 개념적으로 하나인 경우 밸류 타입을 사용하여 표현할 수 있다
+ 밸류는 불변으로 구현하는 것을 권장한다
+ 엔티티의 밸류 타입 데이터를 변경할 때 객체 자체를 완전히 교체 해야 한다

#### 애그리거트
+ 도메인 모델이 복작해 지면 개발자가 한개의 엔티티와 밸류에만 집중하게 된다
+ 큰 수준에서 모델을 이해하지 못하게 된다
+ 이를 해결하기 위해 관련 객체를 하나로 묶은 군집
+ 애그리거트는 군집에 속한 객체들을 관리하는 루트 엔티티를 갖는다

#### 리포지터리
+ 구현을 위한 도메인 모델
+ 애그리거트 단위로 도메인 객체를 저장하고 조회하는 기능을 정의한다
+ 응용 서비스는 필요한 도메인 객체를 구하거나 저장할 때 리포지터리를 사용한다
+ 응용 서비스는 트랜잭션을 관리하는데 트랜잭션 처리는 리포지터리 구현 기술에 영향을 받는다

