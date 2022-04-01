## 검색을 위한 스펙

- 리포지터리는 애그리거트의 저장소이다
- 애그리거트를 저장하고 찾고 삭제하는 것이 리포지터리의 기본 기능이다
- 식별자외에 다양한 조건으로 애그리거트를 찾아야 할 때가 있다

```java
public interface OrderRepository {
    Order findById(OrderNo id);
    List<Order> findByOrderer(String ordererId , Date fromDate , Date toDate);
}
```

- 검색 조건의 조합이 다양해지면 모든 조합별로 find 메서드를 정의할 수 없다
- 검색 조건이 다양한 경우 스펙을 활용하여 문제를 해결해야 한다

## 스펙

- 애그리거트가 특정 조건을 충족하는 지 여부를 검사한다
- agg 파라미터는 검사 대상이 되는 애그리거트 객체이다
- 검사 대상 객체가 조건을 충족하면 true를 반환한다
- 그렇지 않으면 false를 반환한다

```java
public interface Speficiation<T> {
    public boolean isSatisfiedBy(T agg);
}
```

- Order 애그리거트 객체가 특정 고객의 주문인지 확인하는 스펙

```java
public class OrdererSpec implements Specification<Order> {
    
    private String orderId;
    public OrdererSpec(String ordererId) {
        this.ordererId = ordererId;
    }
    
    public boolean isSatisfiedBy(Order agg) {
        return agg.getOrderId().getMemberid().getId().equals(orderId);
    }
}
```

- 리포지터리는 스펙을 전달받아 애그리거트를 걸러내는 용도로 사용한다

```java
public class MemoryOrderRepository implements OrderRepository {
    public List<ORder> findAll(Specification spec) {
        List<Order> allOrders = findAll();
        return allOrders.stream().filter(order -> spec.isSatisfiedBy(order)).collact(toList());
    }
}
```

- 특정 조건을 충족하는 애그리거트를 찾으면 원하는 스펙을 생성해서 리포지터리에 전달해 주면 된다

```java
Specification<Order> orderSpec = new OrderSpec("madvirus");
List<Order> orders = orderRepository.findAll(orderSpec);
```

### 스펙 조합

- 스펙의 장점은 조합에 있다
- 두 스펙을 AND 연산이나 OR 연산자로 조합하여 새로운 스펙을 만들 수 있다
- 조합한 스펙을 다시 조합하여 더 복잡한 스펙을 만들 수 있다

```java
public class AndSpec<T> implements Specification<T> {
    private List<Specification<T>> specs;
    
    public AddSpecification(Specification<T> ... specs) {
        this.specs = Arrays.asList(specs);
    }
}
```

```java
public boolean isSatisfiedBy(T agg) {
    for (Specification<T> spec : specs) {
        if (!spec.isSatisfiedBy(add)) {
            return false;
        }
    }
    return true;
}
```

- AndSpec을 사용하여 여러 스펙을 하나의 스펙으로 만들어 리포지터리에 전달할 수 있다

```java
Specification<Order> ordererSpec = new OrdererSpec("madvirus");
Specification<Order> orderDateSpec = new OrderDateSpec(fromData , toData);
AndSpec<T> spec = new AndSpec(orderSpec, orderDateSpec);
List<Order> orders = orderRepository.findAll(spec);
```

### 블로그 프로젝트에 적용할 수 있는 방법

- 블로그 프로젝트에 필요한 엔티티를 애그리거트로 묶는다
- 다양한 애그리거트를 조합하여 리포지터리에서 조건을 만족하는 지 검사
- AND 또는 OR 연산자로 스펙 조합