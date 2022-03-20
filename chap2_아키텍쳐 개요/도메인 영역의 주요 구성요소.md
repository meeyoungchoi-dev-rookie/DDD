# 아키텍처 개요 - 도메인 영역의 주요 구성요소

## 도메인 영역의 주요 구성요소

`도메인 영역`

- 도메인의 핵심 도멜 구현
- 도메인 영역의 모델은 도메인의 주요 개념을 표현
- 핵심이 되는 로직을 구현

### 엔티티와 밸류

- 도메인 모델의 엔티티와 DB 관계형 모델의 엔티티는 같은 것이 아니다
- 도메인 모델의 엔티티는 데이터와 도메인 기능을 함께 제공한다
- 예) 주문을 표현하는 엔티티는 주문고 관련된 데이터 뿐만 아니라 배송지 주소 변경을 위한 기능도 함께 제공한다

```java
public class Order {
    private OrderNo number;
    private Orderer orderer;
    private ShippingInfo shippingInfo;

    // 도메인 모델 엔티티는 도메인 기능도 함께 제공한다    
    public void changeShippingInfo(ShippingInfo newShippingInfo) {
        ...
    }
}
```

- 도메인 모델의 엔티티는 단순히 데이털르 담고 있는 데이터 구조라기 보다
- 데이터와 함께 기능을 제공하는 객체이다
- 도메인 관점에서 기능을 구현하고 기능 구현을 캡슐롸 해서 데이터가 임의로 변경되는 것을 막는다

- 도메인 모델의 엔티티는 두개 이상의 데이터가 개념적으로 하나인 경우 밸류 타입을 사용하여 표현할 수 있다
- 위의 코드에서 주문자를 표현하는 Orderer는 밸류 타입으로 주문자 이름과 이메일 데이터를 표현할 수 있다

```java
public class Orderer {
    private String name;
    private String email;
    ...
}
```

### RDBMS와 같은 관계형 데이터베이스는 밸류 타입을 제대로 표현하기 힘들다

`한 테이블에 주문자 정보 함께 넣기`

![한 테이블에 주문자 정보 함께 넣기](https://user-images.githubusercontent.com/42866800/159155783-ea10b29a-07e2-41a8-9d70-e0b64fe41b62.png)


- 주문자라는 개념이 들어나지 않는다
- 주문자의 개별데이터만 들어난다

`다른 테이블에 주문자 정보 함께 넣기`

![다른 테이블에 주문자 정보 함께 넣기](https://user-images.githubusercontent.com/42866800/159155791-62d6ea70-7d77-44b0-90b6-5738b2aa3c2c.png)


- 주문자 데이터를 별도 테이블에 저장했다
- 테이블의 엔티티에 가깝지 밸류 타입의 의미가 드러나지 않는다
- 도메인 모델의 ORDERER는 주문자 라는 개념을 잘 반영한다

- 밸류는 불변으로 구현하는 것을 권장한다
- 엔티티의 밸류 타입 데이터를 변경할 때 객체 자체를 완전히 교체한다
- 예) 배송지 정보를 변경하는 코드는 기존 객체의 값을 변경하지 않고 새로운 객체를 필드에 할당한다

```java
pulic class Order {
    private ShippingInfo shippingInfo;
    ...
    
    public void changeShippingInfo(ShippingInfo newShippingInfo) {
        checkShippingInfoChangeable();
        setShippingInfo(newShippingInfo);
    }
    
    private void setShippingInfo(ShippingInfo newShippingInfo) {
        if (newShippingInfo == null) {
            throw new IllegalArgumentException();
        }
        
        this.shippingInfo = newShippingInfo;
    }
}
```

### 애그리거트

- 도메인 도멜에서 전체 구조를 이해하는데 도움이 되는 것
- 관련 객체를 하나로 묶은 군집
- 예)
- 주문이라는 도메인 개념은 주문 , 배송지 정보 , 주문자 , 주문목록 , 총결제 금액의 하위 모델로 구성된다
- 이때 하위 개념을 효현한 모델을 하나로 묶어 주문 이라는 사우이 개념으로 표현할 수 있다
- 

### 애그리거트가 필요한 이유

- 도메인이 커질수록 개발할 도메인 모델로 커지고 많은 엔티티와 밸류가 출현한다
- 엔티티와 밸류 개수가 많아지면 모델은 점점 더 복잡해 진다
- 도메인 모델이 복잡해지면 전체 구조가 아닌 한개 엔티티와 밸류에만 집중하게 되는 경우가 발생한다
- 상위 수준에서 모델을 괄리하기 보다 개별요소에만 초점을 맞추다 보면 큰 수준에서 모델을 이해하지 못해 큰 틀에서 모델을 관리할 수 없게 된다
- 개별 객체뿐만 아니라 상위 수준에서 도멜을 볼 수 있어야 전체 모델의 관계와 개별 모듈을 이해하는데 도움이 된다
- 에그리거트는 군집에 속한 객체들을 관리하는 루트 엔티티를 갖는다
- 루트 엔티티는 에그리거트에 속해 있는 엔티티와 밸류 객체를 사용하여 에그리거트가 구현해야 할 기능을 제공한다
- 

![Order가 애그리거트에 속한 객체를 관리한다](https://user-images.githubusercontent.com/42866800/159155802-d1d22036-6fd4-4489-9ca3-9a32f11c1082.png)

- 애그리거트 루티인 Order는 주문 도메인 로직에 맞게 애그리거트의 상태를 관리한다
- Order의 배송지 정보 변경 기능은 배송지를 변경할 수 있는지 확인한 뒤에 배송지 정보를 변경한다

```java
public class Order {
    private ShippingInfo shippingInfo;
    ...
    
    public void changeShippingInfo(ShippingInfo newShippingInfo) {
        checkShippingInfoChangeable();
        setShippingInfo(newShippingInfo);
    }
    
    private void setShippingInfo(ShippingInfo newShippingInfo) {
        if (newShippingInfo == null) {
            throw new IllegalArgumentException();
        }
        
        this.shippingInfo = newShippingInfo;
    }
    
    private void checkShippingInfoChangeable() {
        ... 배송지 정보를 변경할 수 있는지 여부를 확인하는 도메인 규칙 구성
    }
    
}
```

- 주문 애그리거트는 Order를 통하지 않고 ShippingInfo를 변경할 수 있는 방법을 제공하지 않는다
- 배송지를 변경하려면 루트 엔트리인 Order를 사용해야 하므로 배송지 정보를 변경할 때에는 Order가 구현한 도메인 로직을 항상 따르게 된다

### 리포지터리

- 도메인 객체를 지속적으로 사용하려면 RDBMS, NoSQL, 로컬 파일과 같은 물리적인 저장소에 도메인 객체를 보관해야 한다
- 이를 위한 도메인 모델이 리포지터리 이다
- 엔티티나 밸류가 요구사항에서 도출되는 도메인 모델이라면 리포지토터리는 구현을 위한 도메인 모델이다
- 애그리거트 단위로 도메인 객체를 저장하고 조회하는 기능을 정의한다

```java
public interface OrderRepository {
    public Order findByNumber(OrderNumber number);
    public void save(Order order);
    public void delete(Order order);
}
```

- OrderRepository의 메서드를 보면 대상을 찾고 저장하는 단위가 애그리거트 루트인 Order인 것을 알수 있다
- Order는 애그리거트에 속한 모든 객체를 포함하고 있으므로 결과적으로 애그리거트 단위로 저장하고 조회한다

```java
public class CancelOrderService {
    private OrderRepository orderRepository;
    
    public void cancel(OrderNumber number) {
        Order order = orderRepository.findByNumber(number);
        if (order == null) {
            throw new NoOrderException(number);
        }
        order.cancel();
    }
}
```

- 도메인 모델을 사용해야 하는 코드는 리포지토리를 통해 도메인 객체를 구현한 뒤에 도메인 객체의 기능을 실행하게 된다
- 주문 취소 기능을 제공하는 응용서비스는 OrderRepository를 통해 Order 객체를 구하고 해당 기능을 실행한다
- 도메인 모델 관점에서 OrderRepository는 도메인 객체를 영속화 하는데 필요한 기능을 추상화한 것으로 고수준 모듈에 속한다
- OrderRepository를 구현한 클래스는 저수준 모듈로 인프라스트럭처 영역에 속한다

![리포지토리 인터페이스는 도메인 모델 영역에 속하며 실제 구현 클래스는 인프라스트럭처 영역에 속한다](https://user-images.githubusercontent.com/42866800/159155814-ba07c3b5-8bb9-4fe9-aa24-4bfcc427e228.png)


- 응용 서비스는 의존주입과 같은 방식을 사용하여 실제 리포지터리 구현 객체에 접근한다
- 응용 서비스와 리포지터리는 밀접한 관련이 있다
    - 응용서비스는 필요한 도메인 객체를 구하거나 저장할때 리포지터리를 사용한다
    - 응용서비스는 트랜잭션을 관리하는데 트랜잭션 처리는 리포지토리 구현 기술에 영향을 받는다
- 리포지토리의 사용 주체가 응용 서비스이기 때문에 리포지터리는 응용 서비스가 필요로 하는 기능을 제공한다
    - 애그리거트를 저장하는 메서드
    - 애그리거트 루트 식별자로 애그리거트를 조회하는 메서드
```
public interface SomeRepository {
    void save(Some some);
    Some findById(SomeId id);
}
```