# 아키텍처 개요

## 네 개의 영역

![chap2_네개의 영역](https://user-images.githubusercontent.com/42866800/158821957-5668b065-fbb7-41b8-8090-75d550d48b8e.png)

- `표현`
- HTTP 요청을 응용 영역이 필요로 하는 형식으로 변환해서 응용 영역에 전달한다
- 사용자의 요청을 받아 응용 영역에 전달하고 응용 영역의 처리 결과를 다시 사용자에게 보여준다
- 예) spring mvc 프레임워크
- 표현 영역 사용자 - 웹 브라우저를 사용하는 사람 , REST API를 호출하는 외부 시스템
- 응용 영역의 응답을 HTTP 응답으로 변환해서 전송한다
- 예)
- 웹 브라우저가 HTTP 요청 파라미터로 전송한 데이터를 응용 서비스가 요구하는 형식의 객체 타입으로 변환해서 전달한다
- 응용 서비스가 리턴한 결과를 JSON 형식으로 변환해서 HTTP 응답으로 웹 브라우저에 전달한다

- `응용`
- 표현 영역을 통해 사용자의 요청을 전달받는다
- 시스템이 사용자에게 제공해야 할 기능을 구현한다
- 예) 주문 등록 , 주문 취소 , 상품 상제 조회 같은 기능을 구현한다
- 기능을 구현하기 위해 도메인 영역의 도메인 모델을 사용한다
- 예) 주문 취소 기능을 제공하는 응용 서비스

```java
public class CancelOrderService {
    
    @Transactional
    public void cancelOrder(String orderId) {
        Order order = findORderById(orderId);
        if (order == null) {
            throw new OrderNotFoundException(orderId);
            order.cancel();
        }
    }
    
    ...
}
```

- 응용 서비스는 로직을 직접 수행하기 보다는 도메인 모델에 로직 수행을 위임한다

![chap2_응용영역과_도메인모델](https://user-images.githubusercontent.com/42866800/158822062-13bfb6f9-51d8-499d-b021-c4f121300073.png)

- 즉 , cancelOrder 메서드가 직접 주문 취소 로직을 구현하지 않고
- Order 객체에 취소 처리를 위임한다

- `도메인`
- 도메인 모델을 구현한다
- Order , OrderLine , ShippingInfo 같은 도메인 모델이 여기에 해당한다
- 
- 도메인 모델은 도메인의 핵심 로직을 구현한다
- 주문 도메인의 경우 배송지 변경 , 결제 완료 , 주문 총액 계산 과 같은 핵심 로직을 도메인 모델에서 구현한다

- `인프라스트럭처`

![chap2_인프라스트럭처](https://user-images.githubusercontent.com/42866800/158822134-f53da031-4107-4960-82ff-35e916290550.png)

- 구현 기술에 대한 것을 다룬다
- RDBMS 연동을 처리하고 메시징 큐에 메시지를 전송하거나 수신하는 기능을 구현한다
- 몽고 DB나 HBase를 사용하여 데이터베이스 연동을 처리한다
- SMTP를 이용한 메일 발송 기능을 구현하거나 HTTP 클라이언트를 사용하여 REST API를 호출하는 것도 처리한다
- 즉 , 실제 구현을 다룬다

- 예)
- 응용 영역에서 DB에 보관된 데이터가 필요하면 인프라스트럭쳐 영역의 DB 모듈을 사요하여 데이터를 읽어온다
- 외부에서 메일을 발송해야 하는 경우 인프라스트럭쳐가 제공하는 SMTP 연동 모듈을 사용하여 메일을 발송한다

## 계층 구조 아키텍처

`계층 구조 아키텍처의 구성`

![chap2_계층구조와아키텍처구성](https://user-images.githubusercontent.com/42866800/158823400-da3fbec1-2518-4ba1-b279-34c9eaa678ce.png)

- 계층구조의 특성상 상위 계층에서 하위 계층으로의 의존만 존재하고 하위계층은 상위 계층에 의존하지 않는다
- 표현 계층은 응용 계층에 의존하고
- 응용 계층이 도메인 계층에 의존하지 만
- 반대로 인프라스트럭처 계층이 도메인에 의존하거나
- 도메인이 응용 계층에 의존하지는 않는다

- 계층 구조를 엄격하게 적용하면 상위 계층은 바로 아래의 계층에만 의존을 가져야 한다
- 하지만 구현의  편리함을 위해 계층 구조를 유연하게 적용한다
- 예)
- 응용 계층은 바로 아래 계층인 도메인 계층에 의존하지만
- 외부 시스템과의 연동을 위해 더 아래 계층인 인프라스터럭쳐 계층에 의존하기도 한다

- 계층 구조에 따르면 도메인과 응용 계층을 룰 엔진과 DB 연동을 위해 인프라스터럭쳐 모델에 의존하게 된다

![chap2_계층구조상_의존관계](https://user-images.githubusercontent.com/42866800/158823511-487fd50a-cb45-48f0-98ed-58866bf209a9.png)

- 응용 영역과 도메인 영역은 DB나 외부 시스템 연동을 위해 인프라스트럭쳐의 기능을 사용한다
- 따라서 이런 계층 구조를 사용하는 것은 직관적으로 이해하기 쉽다
- 하지만 표현 , 응용 , 도메인 계층이 상세한 구현 기술을 다루는 인프라스트럭쳐 계층에 종속된다는 것이다

- 예) 도메인 가격 계산 규칙
- 할인 금액 계산 로직이 복잡해지면 객체 지향으로 로직을 구현하는 것보다 룰 엔진을 사용하는 것이 더 알맞을 때가 있다
- 아래는 Drools 라는 룰 엔진을 사용하여 로직을 수행할 수 있는 인프라스트럭쳐 영역의 코드이다

```java
public class DroolsRuleEngine {
    
    private KieContainer kContainer;
    
    public DroolsRuleEngine() {
        kieServices ks = kieServices.Factory.get();
        kContainer = ks.getKieClasspathContainer();
    }
    
    public void evalute(String sessionName , List<?> facts) {
        KieSession kSession = kContainer.newKieSession(sessionName);
        
        try {
            facts.forEach(x -> kSession.insert(x));
            kSession.fireAllRules();    
        
        } finally {
            kSession.dispose();
        }
        
    }
}
```

- 응용 영역은 가격 계산을 위해 인프라스트럭쳐 영역의 DroolsRuleEngine을 사용한다

```java
public class CalculateDiscountService {
    private DroolsRuleEngine ruleEngine;
    
    public CalculateDiscountService() {
        ruleEngine = new DroolsRuleEngine();
    }
    
    public Money calculateDiscount(List<OrderLine> orderLine, String customerId) {
        Customer customer = findCusstomer(customerId);
        
        MutableMoney money = new MutableMoney(0);
        List<?> facts = Arrays.asList(customer, money);
        facts.addAll(orderLines);
        ruleEngine.evaluate("discountCalculation", facts);
        return money.toImmutableMoney();
    }
    
    ...    
}
```

- CalculateDiscountService 는 두가지 문제를 안고 있다
- 첫번째 문제는 CalculateDiscountService  만 테스트 하기 어렵다
- CalculateDiscountService를 테스트 하려면 RuleEngine이 완벽하게 동작해야 한다
- 즉 , RuleEngine 클래스와 관련 설정 파일을 모두 만든 이후에 비로소 CalculateDiscountService 가 올바르게 동작하는지 확인할 수 있다

- 두번째 문제는 구현 방식을 변경하기 어렵다
- 아래의 코드는 Drools에 특화된 코드이다
- 연산결과를 받기 위해 추가한 타입

```java
 MutableMoney money = new MutableMoney(0);
```

- 아래의 코드는 룰 관련 지식이 필요한 코드이다
- 즉 , Drools에 특화된 코드이다

```java
 List<?> facts = Arrays.asList(customer, money);
	facts.addAll(orderLines);
	ruleEngine.evaluate("discountCalculation", facts);
```

- 코드만 보면 Drools가 제공하는 타입을 직접 사용하지 않으므로
- CalculateDiscountService가 Drools 자체에 의존하지 않는다고 생각할 수 있다
- 하지만 discountCalculation 문자열은 Drools의 세션이름을 의미한다
- 즉 , Drools의 세션이름이 변경되면 CalculateDiscountService 코드도 함께 변경해야 한다
- MutableMoney는 룰 적용 결과값을 보관하기 위해 추가한 타입이다
- 다른 방식을 사용했다면 필요 없는 타입이다

- 이처럼 CalculateDiscountService가 겉으로는 인프라스트럭쳐의 기술에 직접적으로 의존하지 않는 것 처럼 보여도
- 실제로는 Drools라는 인프라스트럭쳐 영역의 기술에 완전히 의존하고 있다
- 이런 상황에서 Drools가 아닌 다른 구현 기술을 사용하려면
- 코드의 많은 부분을 고쳐야 한다

`결론`

- 인프라스트럭쳐에 의존하면 테스트의 어려움과 기능 확장의 어려움 문제가 발생한다
- 이를 해결하기 위해서는 DIP를 적용해야 한다

## DIP

- 저수준 모듈이 고수준 모듈에 의존하도록 바꾼다
- 즉 , 추상화한 인터페이스를 사용한다
- 

## 추가적으로 공부해야 하는 부분

메시지큐 - [https://creakycogwheel.tistory.com/entry/캡스톤-프로젝트메시지-큐란](https://creakycogwheel.tistory.com/entry/%EC%BA%A1%EC%8A%A4%ED%86%A4-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%EB%A9%94%EC%8B%9C%EC%A7%80-%ED%81%90%EB%9E%80)

카프카 

SMTP

룰 엔진 - [https://helloino.tistory.com/91](https://helloino.tistory.com/91)