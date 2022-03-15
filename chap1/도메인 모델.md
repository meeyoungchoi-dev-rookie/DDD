# 도메인 모델 시작

## 도메인 : 소프트웨어로 해결하고자 하는 문제 영역

`온라인 서점` 

필요한 기능

- 상품 조회
- 구매
- 결제
- 배송 추적

온라인 서점을 만드는 경우 상품조회 , 구매 , 결제 , 배송 추적 기능을 제공한다

이때 온라인 서점은 도메인이 된다

## 하위 도메인

- 온라인 서점 도메인을 하위 도메인으로 나눌수 있다

![도메인과하위도메인.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/cdaef8e6-ef45-48e2-a028-96ca349c9935/도메인과하위도메인.png)

### 하나의 하위 도메인은 다른 하위 도메인과 연동하여 완전한 기능을 제공할 수 있게 된다

- `카탈로그` - 구매할수 있는 상품목록 제공
- `주문` - 주문 처리
- `혜택` - 쿠폰 , 특별할인 서비스
- `배송` - 고객에게 구매한 상품 전달

`하위 도메인과 모델`

- 도메인은 다수의 하위 도메인으로 구성된다
- 각 하위 도메인이 다루는 영역은 서로 다르다
- 여러 하위 도메인을 하나의 다이어그램에 모델링 하면 안된다
- 에) 카탈로그와 배송 도메인을 구분하지 않고 하나의 다이어그램에 표시하는 경우
    - 상품은 카탈로그의 상품과 배송의 상품 의미를 함께 제공하기 때문에
    - 각 도메인별 상품을 제대로 이해하는데 방해가 된다
    - 모델의 각 구성요소는 특정 도메인을 한정할 때 의미가 완전해 진다
    - 따라서 각 하위 도메인별 모델을 만들어야 한다
    - 즉 , 카탈로그 하위 도메인과 배송 하위 도메인을 따로 만들어야 한다

### 도메인이 제공하는 모든 기능을 구현하지는 않는다

- 예) 배송 시스템은 외부 API를 사용할 수 있다
- 배송 추적에 필요한 기능만 일부 연동
- 결제도 외부 결제 대행 API를 연동하여 사용

### 도메인 마다 고정된 하위 도메인이 존재하지 않는다

- 모든 온라인 쇼핑몰이 혜택을 제공하지 않는다
- 소규모 업체의 경우 엑셀을 사용하여 수작업으로 정산을 처리할 수 있다
- 즉 , 하위 도메인을 어떻게 구성할 지 여부는 상황에 따라 달라진다

## 도메인 모델

- 특정 도메인을 개념적으로 표현한 것
- 예) 주문

![객체기반_주문_도메인_모델](https://user-images.githubusercontent.com/42866800/158388993-44a6dcdb-8426-4a25-95c2-f110fb202adc.png)


### 도메인을 사용하면 여러 사람들이 동일한 도메인을 이해하고 도메인 지식을 공유하는데 도움이 된다

- `주문`
    - 주문번호
    - 총금액
    - 배송정보 변경
    - 주문 취소
    
- `주문자 정보`
    - 주문자명
    
- `배송정보`
    - 배송지 주소
    - 배송 메시지
- `주소`
    - 우편번호
    - 주소1
    - 주소2
- `수취인`
    - 수취인 이름
    - 수취인 전화번호

- `주문 내역`
    - 가격
    - 수량
    - 총 가격

- `상품`
    - 상품명
    - 가격
    - 상세 정보

### 상태 다이어그램을 통한 주문 상태 모델링

- 관계가 중요한 도메인이라면 그래프를 사용하여 도메인을 모델링 할 수 있다

![상태다이어그램을 통한 주문 상태 모델링](https://user-images.githubusercontent.com/42866800/158389086-b03f8dbb-6e72-49c6-bdc5-20d115d34ecf.png)

- 주문을 아직 하지 않은 상태 - 결제 대기 중 - 주문함(결제함) - 여기서 부터는 아래와 같다
- 주문함(결제함) - 상품 준비중 - 출고 완료됨 - 배송시작 - 배송중 - 고객 수령 - 배송 완료됨
- 주문 전 - 결제 대기 중 - 주문 취소함 - 주문 취소됨

### 도메인 모델은 도메인 자체를 이해하기 위한 개념 모델이다

- 개념 모델을 사용하여 바로 코들르 작성할 수 없다
- 구현 기술에 맞는 구현 모델이 따로 필요하다

## 도메인 모델 패턴

`도메인 모델`

- 도메인 계층을 객체지향 기법으로 구현하는 패턴
- 도메인 계층의 객체 모델을 표현할 때도 도메인 모델을 사용한다

`아키텍쳐 구성`

- 애플리케이션 아키텍쳐
    - 표현
    - 응용
    - 도메인
    - 인프라스트럭처
    
- 표현
    - 사용자의 요청을 처리하고 사용자에게 정보를 보여준다
    - 사용자는 소프트웨어를 사용하는 사용자 뿐만 아니라 외부 시스템도 사용자가 될 수 있다
- 응용
    - 사용자가 요청한 기능을 실행한다
    - 업무 로직을 직접 구현하지 않는다
    - 도메인 계층을 조합해서 기능을 실행한다
- 도메인
    - 시스템이 제공하는 도메인의 규칙을 구현한다
- 인프라스트럭처
    - 데이터베이스 메시징 시스템과 같이 외부 시스템과의 연동을 처리한다

[https://happyer16.tistory.com/entry/93-스프링-웹-애플리케이션-아키텍처에-관하여](https://happyer16.tistory.com/entry/93-%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9B%B9-%EC%95%A0%ED%94%8C%EB%A6%AC%EC%BC%80%EC%9D%B4%EC%85%98-%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98%EC%97%90-%EA%B4%80%ED%95%98%EC%97%AC)

```java
public class Order {
    
    private OrderState state;
    private ShippingInfo shippingInfo;
    
    public void changeShippingInfo(ShippingInfo newShippingInfo) {
        if (!state.isShippingChangeable()) {
            throw new IllegalStateException("can't change shipping in " + state );
        }
        
        this.shippingInfo = newShippingInfo;
    }
    
    
    public void changeShipped() {
        // 로직 검사
        this.state = OrderState.SHIPPED;
    }
    
    ...
}
```

```java
public enum OrderState {
    
    PAYMENT_WATING {
        public boolean isShippingChangeable() {
            return true;
        }
        
    },
    
    PREPARING {
        public boolean isShippingChangeable() {
            return true;
        }
    },
    SHIPPED, DELIVERING, DELIVERY_COMPILED;
    
    public boolean isShippingChangeable() {
        return false;
    }
}
```

- `OrderState` - 주문 상태를 나타낸다
    - 주문 대기 중이거나 상품 준비 중인 경우 배송지를 변경하는 것이 가능하다
    - isShippingChangeable() 메서드를 통해 배송지 변경 여부를 검사한다
    - PREPARING - 상품 준비중
    - PAYMENT_WATING - 주문 대기중
    - 배송지를 변경하려면 isShippingChangeable 메서드를 사용하여 변경가능여부를 검사한 후 배송지 변경이 가능하다

- OrderState는 Order에 속한 데이터이므로 배송지 정보 변경가능 여부를 체크하는 메서드를 Order 클래스로 이동 시킬 수도 있다
- 왜? 배송지 변경 가능 여부를 판단할때 주문 상태외 다른 정보가 필요한 경우 isShippingChangeable 메서드를 Order 클래스로 옮기는 것이 좋다

```java
public class Order {
    
    private OrderState state;
    private ShippingInfo shippingInfo;
    
    public void changeShippingInfo(ShippingInfo newShippingInfo) {
        if (!state.isShippingChangeable()) {
            throw new IllegalStateException("can't change shipping in " + state );
        }
        
        this.shippingInfo = newShippingInfo;
    }
    
  
    private boolean isShippingChangeable() {
        return status == OrderState.PAYMENT_WAITING 
        || state == OrderState.PREPARING;
    }
  
    
    public void changeShipped() {
        // 로직 검사
        this.state = OrderState.SHIPPED;
    }
    
    ...
}
```

<aside>
📌 주문과 관련된 주요 업무 로직은 Order 또는 OrderState  도메인에서 구현한다

</aside>

- 그래야 규칙이 바뀌거나 규칙을 확장해야 할 때 다른 코드에 영향을 덜 주고 변경 내역을 모델에 반영할 수 있다

### 개념 모델과 구현 모델

- `개념 모델` - 문제를 분석한 순수한 결과물이다
    - 데이터베이스 , 트랜잭션 처리 , 성능 , 구현 기술과 같은 것들을 고려하지 않는다
    - 실제 코드를 작성할 때 개념 모델을 있는 그대로 사용할 수 없다
    - 따라서 , 개념 모델을 구현 가능한 형태의 모델로 전환하는 과정을 거쳐야 한다
- 프로젝트 초기에는 개요 수준의 개념 모델로 도메인에 대한 전체 윤곽을 이해하고
- 구현하는 과정에서 개념 모델을 구현 모델로 발전시켜 나가야 한다

### 구현을 시작하려면 도메인에 대한 초기 모델이 필요하다

- 모델을 구성하는 핵심 구성요소 , 규칙 , 기능을 찾아야 한다
- 이는 요구사항에서 출발한다
- 예) 주문 관련 요구사항

```java
- 최소 한 종류 이상의 상품을 주문해야 한다
- 한 상품을 한 개 이상 주문할 수 있다
- 총 주문 금액은 각 상품의 구매 가격 합을 모두 더한 금액이다
- 각 상품의 구매 가격 합은 상품 가격에 구매 개수를 곱한 값이다
- 주문할 때 배송지 정보를 반드시 지정해야 한다
- 출고를 하면 배송지 정보를 변경할 수 없다 
- 출고 전에 주문을 취할 수 있다
- 고객이 결제를 완료하기 전에는 상품을 준비하지 않는다
```

요구사항을 통해 4개의 기능을 뽑아 낼 수 있다

- 출고 상태로 변경하기
- 배송지 정보 변경하기
- 주문 취소하기
- 결재 완료로 변경하기

- Order 클래스에 관련 기능을 메서드로 추가

```java
public class Order {
    public void changeShipped() {...}
    public void changeShippingInfo(ShippingInfo newShipping) {...}
    public void cancel() {...}
    public void completePayment() {...}
}
```

`주문 항목 데이터`

```java
- 한 상품을 한 개 이상 주문할 수 있다
- 각 상품의 구매 가격 합은 상품 가격에 구매 개수를 곱한 값이다
```

- 주문 항목을 표현하는 OrderLine 도메인은 주문할 상품 , 상품의 가격 , 구매 개수를 포함하고 있어야 한다
- 각 구매 항목의 구매 가격도 제공해 줘야 한다
- OrderLine 클래스
- 한 상품을 얼마에 몇 개 살지를 필드에 담고 있다
- calculateAmounts() 메서드로 구매 가격을 구한다

```java
public class OrderLine {
    private Product product;
    private int price;
    private int quentity;
    private int amounts;
    
    public OrderLine(Product product, int price, int quantity) {
        this.product = product;
        this.price = price;
        this.quantity = quantity;
        this.amounts = calculateAmounts();
    }
    
    private int calculateAmounts() {
        return price * quantity;
    }
    
    public int getAmounts() {...}
}
```

- Order와 OrderLine 관계를 알려주는 요구사항

```java
- 최소 한 종류 이상의 상품을 주문해야 한다
- 총 주문 금액은 각 상품의 구매 가격 합을 모두 더한 금액이다
```

- Order는 최소 한개 이상의 OrderLine 즉 , 주문정보를 포함해야 한다
- 또한 OrderLine으로 부터 총 주문 금액을 구할 수 있다
- 이를 Order 도메인에 반영하면

```java
public class Order {
    private List<OrderLine> orderLines;
    private int toalAmounts;
    
    public Order(List<OrderLine> orderLines) {
        setOrderLines(orderLines);
    }
    
    private void setOrderLines(List<OrderLine> orderLines) {
        verifyAtLeastOneOrMoreOrderLines(orderLines);
        this.orderLines = orderLines;
        calculateTotalAmounts();
    }
    
    private void verifyAtLeastOneOrMoreOrderLines(List<OrderLine> orderLines) {
        if (orderLines == null || orderLines.isEmpty()) {
            throw new IllegalArgumentException("주문 정보가 없습니다");
        }
    }
    
    private void calculateTotalAmounts() {
        this.totalAmounts = new Money(orderLines.stream().mapToInt(x -> x.getAmounts().getValue()).sum();
    
    }

    public void changeShipped() {...}
    public void changeShippingInfo(ShippingInfo newShipping) {...}
    public void cancel() {...}
    public void completePayment() {...}
}
```

- Order는 한 개 이상의 OrderLines를 가질 수 있으므로 Order를 생성할 때 OrderLine 목록을 List로 전달한다
- 즉 , 주문을 생성할 때 주문 정보를 List에 담아 전달한다

```java
  public Order(List<OrderLine> orderLines) {
        setOrderLines(orderLines);
    }
```

- setOrderLines 메서드를 통해 OrderLine 에 대한 제약조건을 검사한다
- verifyAtLeastOneOrMoreOrderLines 메서드를 사용하여 OrderLine이 한 개이상 존재하는지 검사한다
- calculateTotalAmounts 메서드를 사용하여 총 주문 금액을 계산한다

`배송지 정보`

- 이름
- 전화번호
- 주소

```java
public class ShippingInfo {
    private String receiverName;
    private String receiverPhoneNumber;
    private String shippingAddress1;
    private String shippingAddress2;
    private String shippingZipcode;
}
```

```java
- 주문할 때 배송지 정보를 반드시 지정해야 한다
```

- Order를 생성할 때 OrderLine 과 ShippingInfo 도 함께 전달해야 한다

```java
public class Order {
    private List<OrderLine> orderLines;
    private ShippingInfo shippingInfo;
    private int toalAmounts;
    
    public Order(List<OrderLine> orderLines , ShippingInfo shippingInfo) {
        setOrderLines(orderLines);
        seetShippingInfo(shippingInfo);
    }
    
    private void setOrderLines(List<OrderLine> orderLines) {
        verifyAtLeastOneOrMoreOrderLines(orderLines);
        this.orderLines = orderLines;
        calculateTotalAmounts();
    }
    
    private void setShippingInfo(ShippingInfo shippingInfo) {
        if (shippingInfo == null) {
            throw new IllegalArgumentException("배송 정보가 없습니다.");
        }
        this.shippingInfo = shippingInfo;
    }
    
    
    
    private void verifyAtLeastOneOrMoreOrderLines(List<OrderLine> orderLines) {
        if (orderLines == null || orderLines.isEmpty()) {
            throw new IllegalArgumentException("주문 정보가 없습니다");
        }
    }
    
    private void calculateTotalAmounts() {
        this.totalAmounts = new Money(orderLines.stream().mapToInt(x -> x.getAmounts().getValue()).sum();
    
    }

    public void changeShipped() {...}
    public void changeShippingInfo(ShippingInfo newShipping) {...}
    public void cancel() {...}
    public void completePayment() {...}
}
```

```java
private void setShippingInfo(ShippingInfo shippingInfo) {
    if (shippingInfo == null) {
        throw new IllegalArgumentException("배송 정보가 없습니다.");
    }
    this.shippingInfo = shippingInfo;
}
```

- setShippingInfo 메서드를 통해 배송정보가 null인 경우 예외를 발생시킨다
- 이를 통해 배송지 정보 필수 라는 도메인 규칙을 구현한다

```java
- 출고를 하면 배송지 정보를 변경할 수 없다 
- 출고 전에 주문을 취할 수 있다
```

- 위의 요구사항은 출고 상태가 되기 전과 후의 제약사항을 기술하고 있다
- 출고 상태에 따라 배송지 정보 변경 기능과 주문 취소 기능이 제약을 받는다
- 주문은 출고 상태를 표현할 수 있어야 한다

```java
- 고객이 결제를 완료하기 전에는 상품을 준비하지 않는다
```

- 위의 요구사항을 통해 결제완료 전을 의미하는 상태와 결제 완료
- 또는 상품 준비중 이라는 상태가 필요함을 알수 있다
- Enum을 사용하여 상태정보를 표현할 수 있다

```java
public enum OrderState {
    PAYMENT_WAITING, PREPARING, SHIPPED, DELIVERING,
    DELIVERY_COMPILETED,
    CANCLED;
}
```

- 배송지 변경이나 주문 취소 기능은 출고 전에만 가능하다
- 따라서 , 주문 도메인에 changeShippingInfo 메서드와 cancel()
- 그리고 verifyNotYetShipped 메서드를 추가해 준다

```java
private void changeShippingInfo(ShippingInfo newShipping) {
    verifyNotYetShipped();
    setShippingInfo(newShippingInfo);
}
private void cancel() {
		verifyNotYetShipped();
		this.state = OrderState.CANCLED;
}

private void verifyNotYetShipped() {
    if (state != OrderState.PAYMENT_WATING && state != OrderState.PREPARING) {
        throw new IllegalStateException("이미 배송중이므로 배송지 정볼르 변경할 수 없습니다.");
    }
}
```

- 즉 , 배송지 정보를 변경가능한 지 검사한다
- 주문상태가 결제 대기중 이거나 상품준비중인 경우에만 배송정보 변경이 가능하다

## 핵심

- 제약조건을 검사하는 메서드 이름이 isShippingChangeable 에서 verifyNotYetShipped 로 변경되었다
- 최조체는 배송지 정보 변경에 대한 제약 조건만 파악했기 때문에
- 배송지 정보 변경가능 여부만 체크 했으면 됬다
- 그런데 배송지 정보 변경과 주문 취소가 둘다 상품 출고전에 만 가능하다는 조건이 추가되어 메서드 이름을 verifyNotYetShipped로 변경했다

<aside>
📌 모델을 공유할 때는 화이트보드나 위키와 같은 도구를 사용하여 누구나 쉽게 접근할 수 있도록 하는 것이 좋다

</aside>

## 문서화

- 지식을 공유하기 위해 문서화를 한다
- 전반적인 기능 목록이나 모듈 구조 , 빌드 과정은 상위 수준에서 정리한 문서를 참조하는 것이 더 도움이 된다
- 전체 구조를 이해하고 더 깊게 이해할 필요가 있는 부분을 코드로 분석해 나가면 된다
- 코드를 보면서 도메인을 깊게 이해하게 되므로 코드 자체도 문서화의 대상이 된다
- 도멩니 지식이 잘 묻어나도록 코드를 작성하지 않으면 도메인 관점에서 왜 코드를 그렇게 작성했는지 이해하는데 도움이 되지 않는다

<aside>
📌 도메인 관점에서 코드가 도메인을 잘 표현해야 코드의 가독성이 높아지며 문서로서 코드가 의미를 갖는다

</aside>

[엔티티와 밸류](https://www.notion.so/360fa859372c49a296a0b6d6449a64ef)

- Enum
    - 열거형
    - 서로 연관된 상수들의 집합
    - final static String , final static int 와 같이 문자열 이나 숫자를 나타내는 기본 자료형 값을 Enum으로 대체하여 사용할 수 있다
    - `특징`
    - 클래스를 상수 처럼 사용할 수 있다
    - 다른 패키지나 클래스에서 Enum 타입에 접근하여 동적으로 값을 정해줄 수 없다
    - 왜? 컴파일 타입에 모든 값이 결정되기 때문이다
    - 인스턴스 생성과 상속을 방지하여 상수값의 타입 안정성이 보장된다
    - [https://math-coding.tistory.com/179](https://math-coding.tistory.com/179)