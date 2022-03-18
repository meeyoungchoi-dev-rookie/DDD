## DIP

- 가격 할인 계산을 하려면 고객 정보를 구해야 하고
- 구현 고객 정보와 주문 정보를 사용하여 룰을 실행해야 한다
- 

![dip_고수준 모듈 저수준 모듈](https://user-images.githubusercontent.com/42866800/159007157-dab22f69-d624-4f2c-a365-e9e692651cc5.png)


- CalculateDiscountService는 고수준 모듈이다
- `고수준 모듈`
- 의미있는 단일 기능을 제공하는 모듈
- CalculateDiscountService는 가격 할인 계산 이라는 기능을 구현한다
- 고수준 모듈의 기능을 구현하려면 여러 하위 기능이 필요하다
- 가격 할인 계산 기능을 구현하려면 고객 정보를 구해야 하고
- 룰을 실행해야 한다
- 이 두 기능이 하위 기능이다
- `저수준 모듈`
- 하위 기능을 실제로 구현한 것

- 고수준 모듈이 제대로 동작하려면 저수준 모듈을 사용해야 한다
- 고수준 모듈이 저수준 모듈을 사용하면 구현 변경과 테스트가 어려워지는 문제가 발생한다
- `DIP`
- 저수준 모듈이 고수준 모듈에 의존하도록 바꾼다
- 즉 , 인터페이스를 추상화 시킨다

- CalculateDiscountService 입장에서 룰 적용을 Drools로 구현했는지 자바로 구현했는지 중요하지 않다
- 고객 정보와 구매 정보에 룰을 적용하여 할인 금액을 구하는 것이 중요하다

```java
public interface RuleDiscounter {
    public Money applyRules(Customer customer, List<OrderLine> orderLines);
}
```

- CalculateDiscountService 가 RuleDiscounter를 사용하도록 변경
- CalculateDiscountService 가 Drools에 의존하는 코드를 포함하고 있지 않다
- 단지 RuleDiscounter가 룰을 적용한다는 것만 알 뿐이다
- 실제 RuleDiscounter 구현체는 생성자를 통해 전달받는다

```java
public class CalculateDiscountService {
    private RuleDiscounter ruleDiscounter;
    
    public CalculateDiscountService(RuleDiscounter ruleDiscounter) {
        this.ruleDiscounter = ruleDiscounter;
    }
    
    public Money calcualteDiscount(List<OrderLine> orderLine, String customerId) {
        Customer customer = findCustomer(customerId);
        return ruleDiscounter.applyRules(customer, orderLines);
    }
    
    ...    
}
```

- 룰 적용을 구현한 클래스는 RuleDiscounter 인터페이스를 구현한다

```java
public class DroolsRuleDiscounter implements RuleDiscounter {
    private KieContainer kContainer;
    
    public DroosRuleDiscounter() {
        KieServices ks = kieServices.Factory.get();
        kContainer = ks.getKieClasspathContainer();
    }
    
    public Money applyRule(Customer customer , List<OrderLine> orderLines) {
        kieSession kSession = kContainer.newKieSession("discountSession");
        try {
            ...
            kSession.fireAllRules();
        } finally {
            kSession.dispose();
        }
        
        return money.toImmutableMoney();
        
    }
}
```

![DIP를 적용한 구조](https://user-images.githubusercontent.com/42866800/159007054-81cb97b2-78b4-4320-9f3a-ced5c39a41e3.png)

- DIP를 적용하면서 다른 영역이 인프라ㅡ트럭처 영역에 의존할 때 발생했던 문제를 해결할 수 있다

1. 구현 기술 교체 문제

  

- 고수준 모듈은 구현을 추상화한 인터페이스에 의존한다
- 실제 사용할 저수준 구현체는 의존 주입을 받아 사용할 수 있다

```java
// 사용할 저수준 객체 생성
RuleDiscounter ruleDiscounter = new DroolsRuleDiscounter();

// 생성자 방식으로 주입
CalculateDiscountService disService = new CalculateDiscountService(ruleDiscounter);
```

- 구현 기술을 변경하는 경우 CalculateDiscountService를 수정할 필요가 없다
- 저수준 구현 객체를 생성하는 코드만 변경하면 된다

```java
// 사용할 저수준 구현 객체 변경
RuleDiscounter ruleDiscounter = new SimpleRuleDiscounter();

// 사용할 저수준 모듈을 변경해도 구수준 모듈을 수정할 필요가 없다
CalculateDiscountService disService = new CalculateDiscountService(ruleDiscounter);
```

1. 테스트의 문제
- CalculateDiscountService 테스트
- 만약 CalculateDiscountService 가 저수준 모듈에 직접 의존했다면
- 저수준 모듈이 만들어지기 전까지 테스트를 할 수 없다
- 하지만 CustomerRepository 와 RuleDiscounter 는 인터페이스 이므로 대용 객체를 사용하여 테스트를 진행할 수 있다

```java
public class CalculateDiscountService {
    
    private CustomerRepository customerRepository;
    private RuleDiscounter ruleDiscounter;
    
    public CalculateDiscountService(CustomerRepository customerRepository, RuleDiscounter ruleDiscounter) {
        this.customerRepository = customerRepository;
        this.ruleDiscounter = ruleDiscounter;
    }
    
    public Money calculateDiscount(OrderLine orderLines, String customerId) {
        Customer customer = findCustomer(customerId);
        return ruleDiscounter.applyRules(customer, orderLines);
    }
    
    private Customer findCustomer(String customerId) {
        Customer customer = customerRepository.findById(customerId);
    
        if (customer == null) {
            throw new NoCustomerException();
            
        }

        return customer;
                
    }

    ...
}
```

- stubRepo 와 stubRule은 각각 CustomerRepository와 RuleDiscounter의 대용 객체이다
- stubRepo는 Mockito라는 Mock 프레임워크를 사용하여 대용 객체를 생성했다
- stubRule는 메서드가 한 개여서 람다식을 사용하여 객체를 생성했다
- 두 대용 객체는 테스트를 수앻나느 데 필요한 기능만 수행한다

```java
public class CalculateDiscountServiceTest {
    
    public voind noCustomer_thenExceptionShouldBeThrown() {
        
        CustomerRepository stubRepo = mock(CustomerRepository.class);
        when(stubRepo.findById("noCustId")).thenReturn(null);
        
        RuleDiscounter stubRule = (cust, lines) -> null;
        
        CalculateDiscountService calDisSvc = new CalculateDiscountService(stubRepo, stubRule);
        calDisSvc.calculateDiscount(someLines, "noCustId");
        
    }
}
```

- stubRepo의 경우 findByid(”noCustId”)를 실행하면 null을 리턴한다
- calDisSvc  객체를 생성할 때 생성자로 stubRepo를 주입받는다
- 따라서 calDisSvc.calculateDiscount(someLines, "noCustId"); 를 실행하면
- findById() 메서드에서 실행하는 customerRepository.findById(customerId) 코드는 null을 리턴하고
- 결과적으로 NoCustomerException을 발생시킨다

`실제 구현 없이 테스트 할 수 있는 이유`

- DIP를 적용하여 고수준 모듈이 저수준 모듈에 즤존하지 않도록 했기 때문이다

### DIP 주의사항

![잘못된 DIP](https://user-images.githubusercontent.com/42866800/159007216-b0e552d1-2820-473c-bca2-4d4a09887e55.png)

- 도메인 영역은 구현 기술을 다루는 인프라스트럭처 영역에 의존하고 있다
- 여전히 고수준 모듈이 저수준 모듈에 의존하고 있는 것이다
- RuleEngine 인퍼페이스는 고수준 모듈인 도메인 관점이 아니라 룰 엔진 이라는 저수준 모듈 관점에서 도출한 것이다
- DIP를 적용할 때 하위 기능을 추상화한 인터페이스는 고수준 모듈 관점에서 도출해야 한다
- CalculateDiscountService 입장에서 봤을때 할인 금액을 구하기 위해 룰 엔진을 사용하는지 직접 연산하는 지 여부는 중요하지 않다
- 단지 규칙에 따라 할인 금액을 계산하는 것이 중요할 뿐이다
- 따라서 할인 금액 계산을 추상화한 인터페이스는 고수준 모듈에 위치해야 한다

![하위 기능을 추상화한 인터페이스는 고수준 모듈에 위치한다](https://user-images.githubusercontent.com/42866800/159007299-e8d97528-2f58-46fb-9eff-c15828ca9c0f.png)

### DIP와 아키텍처

- 인프라스트럭처 영역은 구현 기술을 다루는 저수준 모듈이다
- 응용 영영과 도메인 영역은 고수준 모듈이다
- 인프라스트럭처에 DIP를 적용하면
- 인프라스트럭처 영역이 응용 영역과 도메인 영역에 의존하는 구조가 된다

![아키텍처에 DIP를 적용하는 경우](https://user-images.githubusercontent.com/42866800/159007347-9491971e-d299-455c-addd-7c4daa40d4bc.png)

- 즉 , 도메인과 응용 영역에 영향을 주지 않으면서 구현 기술을 변경하는 것이 가능해 진다
- 예)

![DIP를 적용한 구조2](https://user-images.githubusercontent.com/42866800/159007414-72f4be04-81bb-430e-b2be-44976b0482fd.png)

- 인프라스트럭처 영역의 EmailNotifier 클래스는 응용 영역의 Notifier 인터페이스를 상속받고 있다
- 주문시 통시 방식에 SMS를 추가해야 하는 경우 인프라스트럭처 영역에 Notifier 인터페이스를 상속받은 클래스 추가해 주면 된다
- 즉 , OrderService를 변경할 필요가 없다

- MyBatis 대신 JPA를 구현기술로 변경하는 경우
- OrderRepository를 상속받은 클래스를 인프라스트럭처 영역에 추가해 주면된다

### DIP 정리

- 저수준 모듈이 고수준 모듈에 의존한다
- 자신보다 변하기 쉬운것에 의존하지 않는다
- 자신보다 변하기 쉬운 것에 의존하던 것을 추상화된 인터페이스나 상위 클래스를 두어 변하기 쉬운 것의 변화에 영향받지 않게 한다

- 스노우타이어가 아닌 다른 타이어로 자동차가 갈아껴야 한다면
- 자동차 클래스의 전체 코드를 변경해야 한다

![dip_적용전](https://user-images.githubusercontent.com/42866800/159007475-1cf0989a-45fc-423d-8e5f-52c22d447581.png)

- 자동차가 의존하고 있는 타이어가 변경되어도 타이어 인터페이스를 구현한 구현체만 바꿔주면 되기 때문에 자동차 클래스는 건드리지 않아도 된다

![DIP 적용후](https://user-images.githubusercontent.com/42866800/159007516-2aad0b88-199b-4b20-8aed-fc89379ff781.png)

## 참고 

[https://server-engineer.tistory.com/228?category=643527](https://server-engineer.tistory.com/228?category=643527)

[https://steady-coding.tistory.com/388](https://steady-coding.tistory.com/388)


## 블로그 프로젝트에 DIP를 적용할 수 있는 방법
+ DAO용 mapper 인터페이스와 mapper.xml 파일에 DIP를 적용한다
+ DAO는 도메인 패키키 하위에 만들고 mapper.xml 파일의 경우 resources 폴더 밑에 인프라스트럭처 디렉토리를 만들어 파일을 생성한다
+ 서비스가 DAO에 의존하면 안된다
+ 따라서 mapper 인터페이스 구현체를 만들어 mapper 인터페이스를 상속받고 서비스단에서는 mapper 인터페이스를 DI하여 사용한다
+ 다른 인터페이스 구현체가 추가되거나 인터페이스 구현체가 변경되는 경우 서비스단 코드는 변경하지 않아도 된다