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