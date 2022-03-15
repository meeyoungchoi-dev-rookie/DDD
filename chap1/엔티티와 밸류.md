# 엔티티와 밸류

# 모델은 엔티티와 밸류로 구분할 수 있다

- 요구사항에서 도출한 주문 도메인 모델은 엔티티와 밸류로 구분된다

![객체기반_주문_도메인_모델](https://user-images.githubusercontent.com/42866800/158388993-44a6dcdb-8426-4a25-95c2-f110fb202adc.png)

- 엔티티와 밸류를 제대로 구분해야 도메인을 올바르게 설계하고 구현할 수 있다

### 엔티티

- 식별자를 갖는다
- `식별자` - 엔티티 객체마다 고유해서 각 엔티티는 서로 다른 식별자를 갖는다
- 예) 주문 도메인에서 주문은 주문번호를 갖는다
- 주문번호는 각 주문마다 서로 다르다
- 따라서 주문번호가 주문 식별자가 된다
- 즉  , 주문에 해당하는 클래스가 Order 이므로 Order가 엔티티가 되어 주문번호를 속성으로 갖게 된다
- 엔티티의 식별자는 바뀌지 않는다
- 엔티티를 생성하고 엔티티의 속성을 바꾸고 엔티티를 삭제할 때까지 식별자는 유지된다

- 엔티티 객체의 식별자가 같으면 두 엔티티는 같다고 판단할 수 이싿
- 엔티티를 구현한 클래스는 equals 메서드와 hashCode 메서드를 구현할 수 있다

```java
public class Order {
    ...
    private String orderNumber;
  
    @Override
    public boolean equals(Object obj) {
        if (this === obj) {
            return true;
        }
        
        if (obj == null) {
            return false;
        }
        
        if (obj.getClass() != Order.class) {
            return false;
        }
        
        Order order = (Order)obj;
        if (this.orderNumber == null) {
            return false;
        }
        return this.orderNumber.equals(other.orderNumber);
    }
    
    @Override
    public int hashCode() {
        final in t prime = 31;
        int result = 1;
        result = prime * result + ((orderNumber == null) ? 0 : orderNumber.hashCode());
        return result;
    }
}
```

### 엔티티의 식별자 생성

- 엔티티의 식별자를 생성하는 시점은 도메인의 특징과
- 사용하는 기술에 따라 달라진다
- `식별자 생성 방법`

```java
- 특정 규칙에 따라 생성
- UUID 사용
- 값을 직접 입력
- 일련번호 사용 (시퀀스나 DB의 자동 증가 컬럼 사용)
```

- 주문번호 운송장번호 가드번호와 같은 식별자는 특정 규칙에 따라 생성한다
- 규칙은 도메인에 따라 다르고 같은 주문번호라도 회사마다 다르다
- 흔히 사용하는 규칙은 현재 시간과 다른 값을 함께 조합하는 것이다
- 시간을 이용하여 식별자를 생성할 때 주의할 점은 같은 시간에 동시에 식별자를 생성할 때 같은 식별자가 만들어지면 안 된다는 것이다

`UUID`

- 마땅한 규칙이 없다면 UUID를 식별자로 사용해도 된다
- 자바의 경우 java.util.UUID 클래스를 사용하여 UUID를 생성할 수 있다

```java
UUID uuid = UUID.randomUUID();
String strUuid = uuid.toString();
```

- 회원의 아이디나 이메일과 같은 식별자는 값을 직접 입력한다
- 사용자가 직접 입력하는 값이기 때문에 식별자를 중복해서 입력하지 않도록 사전에 방지하는 것이 중요하다

`일련번호`

- 식별자로 일련번호를 사용하기도 한다
- 주로 데이터베이스가 제공하는 자동 증가 기능을 사용한다
- 오라클을 사용하는 경우 시퀀스를 사용하여 자동 증가 식별자를 구한다
- MySQL을 사용하는 경우 자동 증가 칼럼을 사용하여 일련변호 식별자를 생성한다
- 자동 증가 컬럼을 제외한 다른 방식은 식별자를 먼저 만들고 엔티티 객체를 생성할 때 식별자를 전달할 수 있다

```java
String orderNumber = orderRepository.generate();

Order order = new Order(orderNumber,...);
orderRepository.save(order);
```

- 자동 증가 컬럼의 경우 DB 테이블에 데이터를 삽입해야 비로소 값을 알수 있다
- 테이블에 데이터를 추가하기 전에는 식별자를 알 수 없다
- 즉 , 엔티티 객체를 생성할 때 식별자를 전달할 수 없음을 뜻한다

```java
Article article = new Article(author, title, ...);
articleRepository.save(article);
Long savedArticleId = article.getId();
```

`Repository`

- 도메인 객체를 데이터베이스에 저장할 때 사용하는 구성요소
- 자동 증가 컬럼을 사용할 경우 리포지토리 라는 DB가 생성한 식별자를 구해서 엔티티 객체에 반영해 준다

### 밸류 타입

- ShippingInfo 클래스는 받는 사람 과 주소에 대한 데이터를 갖고 있다

```java
public class ShippingInfo {
    private String receiverName;
    private String receiverPhoneNumber;
    
    private String shippingAddress1;
    private String shippingAddress2;
    private String shippingZipcode;
}
```

- receiverName 필드와 receiverPhoneNumber 필드는 서로 다른 두 데이터를 담고 있지만 개념적으로 받는 사람을 의미한다
- 두 필드는 실제로 한 개의 개념을 표현하고 있다
- 또한 shippingAddress1 , shippingAddress2 , shippingZipcode 필드는 주소라는 하나의 개념을 표현한다

- 밸류 타입은 개념적으로 완전한 하나를 표현할 때 사용한다
- 예) 받는 사람을 위한 밸류 타입인 Receiver
- Receiver는 받는 사람 이라는 도메인 개념을 표현한다
- 그 자체로 받는 사람을 뜻한다
- 밸류 타입을 사용함으로써 개념적으로 완전한 하나를 표현할 수 있게 된다

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

- 밸류 타입으로써 완전한 하나의 주소를 표현하게 된다

```java
public class Address {
    private String address1;
    private String address2;
    private String zipcode;
}
```

- 밸류 타입을 사용하여 ShippingInfo 클래스를 다시 구현

```java
public class ShippingInfo {
    private Receiver receiver;
    private Address address;
    
    ... 생성자 ,  get 메서드
}
```

- 밸류 타입이 꼭 두개 이상의 데이터를 가져야 하는 것은 아니다
- 의미를 명확하게 표현하기 위해 밸류 타입을 사용하는 경우도 있다

```java
public class OrderLine {
    private Product product;
    private int price;
    private int quantity;
    private int amounts;
}
```

- OrderLine의 price와 amounts는 int 타입의 숫자를 사용하고 있다
- 하지만 이들이 의미하는 것은 돈이다
- 따라서 돈을 의미하는 Money 타입을 만들어 사용하면 코드를 이해하는 데 도움이 된다

```java
public class Money {
    private int value;
    
    public Money(int value) {
        this.money = money;
    }
    
    public int getValue() {
        return this.value;
    }
}
```

- Money를 사용하여 OrderLine 도메인을 변경한 코드
- Money 타입덕분에 price 나 amounts가 금액을 의미한다는 것을 쉽게 알 수 있다

```java
public class OrderLine {
    private Product product;
    private Money price;
    private int quantity;
    private Money amounts;
}
```

- 밸류 타입을 사용하면 밸류 타입을 위한 기능을 추가할 수 있다
- 예) Money 밸류에 돈 계산을 위한 기능을 추가할 수 있다
- Money를 사용하는 코드는 정수 타입 연산이 아닌 돈 계산 이라는 의미가 명확한 코드를 작성할 수 있게 된다
- 즉 , 코드의 가독성이 높아진다

```java
public class Money {
    private int value;
	    
		... 생성자 , getValue()		

    public Money add(Money money) {
        return new Money(this.value + money.value);
    }

    public Money multiply(int multiplier) {
        return new Money(value * multiplier);
    }
}
```

### 밸류 객체의 데이터 변경

- 기존 데이터를 변경하기 보다는 변경한 데이터를 갖는 새로운 밸류 객체를 생성하는 방식을 선호한다
- 예) Money 클래스의 add 메서드를 보면 Money를 새로 생성하고 있다
- value를 변경할 수 있는 메서드가 없다
- Money 처럼 데이터 변경 기능을 제공하지 않는 타입을 불변(immutable)이라고 표현한다
- 불변 타입을 사용하면 보다 안전한 코드를 작성할 수 있다

- OrderLine 밸류에서 Money 객체를 전달 받는데 Money 가 setValue 와 같은 메서드를 제공하여 값을 변경할 수 있게 된다면?
- price 값이 잘못 반영되는 상황이 발생하게 된다

```java
Money price = new Money(1000);
OrderLine line = new OrderLine(product, price, 2);
price.setValue(2000);
```

- 이런 문제가 발생하지 않도록 OrderLine 생성자가 새로운 Money 객체를 생성하도록 코드를 작성해야 한다
- 즉  , Money가 불변 객체가 아니기 때문에
- price 파라미터가 변경될때 발생하는 문제를 방지하기 위해
- 데이터를 복사한 새로운 객체를 생성해야 한다
- [https://mititch.tistory.com/77](https://mititch.tistory.com/77)

```java
public class OrderLine {
    private Money price;
    
    public OrderLine(Product product, Money price, int quantity) {
        this.product = product;
        this.price = new Money(price.getValue());
        this.quantity = quantity;
        this.amounts = calculateAmounts();
    }
}
```

- 불변 객체는 잠조 투명성과 스레드에 안전한 특징을 갖고 있다
- [https://velog.io/@max9106/도서모던-자바-인-액션-함수형-관점으로-생각하기](https://velog.io/@max9106/%EB%8F%84%EC%84%9C%EB%AA%A8%EB%8D%98-%EC%9E%90%EB%B0%94-%EC%9D%B8-%EC%95%A1%EC%85%98-%ED%95%A8%EC%88%98%ED%98%95-%EA%B4%80%EC%A0%90%EC%9C%BC%EB%A1%9C-%EC%83%9D%EA%B0%81%ED%95%98%EA%B8%B0)
- 주문정보에 따라 가격이 변경되는 경우 set 메서드를 통해 변경하지 않고 새로운 Money 객체를 만드는 것이 안전하다

### 밸류 객체가 같은 지 비교하는 방법

- 모든 속성이 같은지 비교해야 한다
- [https://codechacha.com/ko/java-compare-objects/](https://codechacha.com/ko/java-compare-objects/)
- [https://codevang.tistory.com/103](https://codevang.tistory.com/103)

```java
public class Receiver {
    private String name;
    private String phoneNumber;
    
    public boolean equals(Object other) {
        if (other == null) {
            return false;
        }
        
        if (this == other) {
          return true;  
        } 
        
        if (!(other instanceof Receiver)) {
            return false;
        }
        
        Receiver that = (Receiver)other;
        return this.name.equals(that.name) && this.phoneNumber.equals(that.phoneNumber);
    }
}
```

### 엔티티 식별자와 밸류 타입

- 엔티티 식별자의 실제 데이터는 String과 같은 문자열로 구성된 경우가 많다
- 식별자는 단순한 문자열이 아닌 도메인에서 특별한 의미를 지니는 경우가 많다
- 식별자를 위한 밸류 타입을 사용하여 의미가 잘 들어나도록 할 수 있다
- 예) 주문번호를 표현하기 위해 Order의 식별자 타입으로 String 대신 OrderNo 밸류 타입을 사용하면 타입을 통해 해당 필드가 주문번호 라는 것을 알 수 있다

```java
public class Order {
    private OrderNo id;
    ...
    public OrderNo getId() {
        return id;
    }
}
```

- OrderNo 대신에 String 타입을 사용하면 id라는 이름만으로 해당 필드가 주문번호인지 여부를 알 수 없다
- 필드의 의미가 드러나도록 하려면 orderNo 라는 필드 이름을 사용해야 한다
- 식별자를 위해 OrderNo 타입을 만들면 타입 자체로 주문번호 라는 것을 알수 있으므로 필드 이름이 id여도 실제 의미를 찾는 것은 어렵지 않다

### 도메인 모델에 set 메서드 넣지 않기

- 도메인 모델에 get/set 메서드를 추가하는 것은 좋지 않다
- set 메서드는 도메인의 핵심 개념이나 의도를 코드에서 사라지게 한다

- Order 도메인에 set 메서드가 있다면

```java
public class Order {
    ...
    public void setShippingInfo(ShippingInfo newShipping) {...}
    public void setOrderState(OrderState state) {...}
}
```

- setShippingInfo 메서드는 단순히 배송지 값을 설정한다는 의미를 가진 메서드이다
- changeShippingInfo 메서드는 배송지 정보를 새로 변경한다는 의미를 가졌다

- setOrderState 메서드는 단순히 주문 상태 값을 설정한다는 것을 의미한다
- completePayment 메서드는 결제를 완료했다는 의미를 갖는다
- 구현할 때도 결제 완료와 관련된 처리 코드를 구현하기 때문에
- 결제 완료와 관련된 도메인 지식을 코드로 구현하는 것이 자연스럽다
- setOrderState는 상태 값만 변경할 지 아니면 상태 값에 따라 다른 처리를 위한 코드를 함께 구현할 지 애매하다

- set 메서드의 또 다른 문제는 도메인 객체를 생성할 때 완전한 상태가 아닐수 있다는 것이다

```java

public class Order {
    public Order(Orderer orderer, List<OrderLine> orderLines , ShippingInfo shippingInfo , OrderState state) {
        setOrderer(orderer);
        setOrderLines(orderLines);
    }
    
    private void setOrderer(Orderer orderer) {
        if (orderer == null) {
            throw new illegalArgumentException("주문자 정보 없음");
        }
        this.orderer = orderer;
    }
    
    private void setOrderLines(List<OrderLine> orderLines) {
        verifyAtLeastOneOrMoreOrderLines(orderLines);
    }
    
    private void verifyAtLeastOneOrMoreOrderLines(List<OrderLine> orderLines) {
        if (orderLines == null || orderLines.isEmpty()) {
            throw new IllegalArgumentException("주문 내역 없음");
        }
    }
    
    
    private void calculateTotalAmounts() {
        this.totalAmounts = orderLines.stream().mapToInt(x -> x.getAmounts()).sum();
    }
}
```

- set 메서드의 접근범위가 private 이다
- 이는 클래스 내부에서 데이터를 변경할 목적으로 사용된다
- private 이기 때문에 외부에서 데이터를 변경할 목적으로 set 메서드를 사용할수 없다

```java
 private void setOrderer(Orderer orderer) {
    if (orderer == null) {
        throw new illegalArgumentException("주문자 정보 없음");
    }
    this.orderer = orderer;
}
    
```

- set 메서드를 구현해야 할 특별한 이유가 없다면 불변 타입의 장점을 살릴수 있도록 밸류 타입을 불변으로 구현한다

### DTO의 get/set 메서드

- DTO는 Data Transfer Object의 약자이다
- 프레젠테이션 계층과 도메인 계층이 데이터를 서로 주고받을때 사용하는 구조체이다
- 프레임워크가 필드에 직접 값을 할당하는 기능을 제공하고 있다면
- set 메서드를 만드는 대신 해당 기능을 최대한 활용해야 한다
- DTO도 불변 객체가 되어 불변의 장점을 DTO 까지 확장할 수 있게 된다

### 도메인 용어

- 코드를 작성할 때 도메인에서 사용하는 용어는 매우 중요하다
- 도메인에서 사용하는 용어를 코드에 반영하지 않으면 코드의 의미를 해석하는데 부담을 주게 된다

```java
public OrdderState {
    STEP1 , STEP2 , STEP3 , STEP4 , STEP5 , STEP6
}
```

- OrderState를 위와 같이 구현한 경우
- 실제주문 상태는 결제대기중 , 상품 준비중 , 출고완료됨 , 배송중 , 배송완료됨 , 주문 취소됨 인데
- 위의 코드는 전체 상태를 6단계로 보고 코드로 표현한 것이다

```java
public OrdderState {
    STEP1 , STEP2 , STEP3 , STEP4 , STEP5 , STEP6
}

public class Order {
    public void changeShippingInfo(ShippinInfo newShippingInfo) {
        verfyStemp1OrStemp2();
    }
    
    private void verifyStep1OrStep2() {
        if (state != orderState.STEP1 && state != OrderState.STEP2) {
            throw new IllegalStateException("이미 배송중 입니다");
        }
    }
}
```

- 배송지 변경은 출고전 에 가능한데 verifyStep1OrStep2라는 이름은 도메인의 주요 규칙이 드러나지 않는다
- 즉 출고전 이란느 단어를 사용하면 머릿속에서 출고전은 STEP1과 STEP2 라고 도메인 지식을 코드로 해석해야 한다

- 하지만 아래와 같이 작성하게 되면 불필요한 변환 과정을 거치지 않아도 된다

```java
public enum OrderState {
    PAYMENT_WAITING, PREPARING, SHIPPED, DELIVERING< DELIVERY_COMPLETED;
}
```

- 즉 , 도메인에서 사용하는 용어를 최대한 코드에 반영하면 코드를 도메인 용어로 해석하거나 도메인 용어를 코드로 해석하는 과정이 줄어든다
- 이는 코드의 가독성을 높여 코드를 분석하고 이해하는 시간을 절약한다
- 또한 도메인 용어를 사용해서 최대한 도멩니 규칙을 코드로 작성하게 되므로 의미를 변환하는 과정에서 발생하는 버그도 줄어들게 된다