- 리포지터리에서 모든 애그리거트를 조회한 후 스펙을 사용해서 걸러내면
- 실행 속도 문제가 생긴다
- 예)
- 애그리거트가 10만 개인 경우 10만 개 데이터를 DB에서 메모리로 로딩한 후
- 다시 10만개 객체를 루프를 돌면서 스펙을 검사해야 한다
- 시스템 성능을 느리게 만든다

## 해결책

- 쿼리에 where 조건을 붙여 필요한 데이터만 걸러내야 한다
- 즉 메모리에서 걸래내는 방식에서 쿼리의 where를 사용하는 방식으로 변경해야 한다
- CreaterialBuilder와 Predicate를 사용하여 검색조건을 구할 수 있다

### JPA 스펙 구현

```java
public interface Specification<T> {
    Predicate toPredicate(Root<T> root , CriteriaBuilder cb);
}
```

```java
public class OrderSpec implements Specification<Order> {
    
    private String ordererId;
    
    public OrderSpec(String ordererId) {
        this.ordererId = ordererId;
    }
    
    public Predicate toPredicate(Root<ORder> root, CriteriaBuilder cb) {
        return cb.equal(root.get(Order_.order)
                            .get(Orderer_.memberId).get(MemberId_.id), ordererId);
    }
}
```

- OrdererSpec 클래스의 toPredicate 메서드를 통해 Order 객체의 memberId 프로퍼티와 생성자로 전달받은 ordererId가 같은지 비교한다
- Predicate 객체가 Order 객체의 프로퍼티와 orderid가 같은 지 비교해 준다
- 그러면 응용서비스에선느 스펙을 생성하고 리포지터리에서 필요한 애그리거트를 검색하면 된다

- 스펙생성에 필요한 코드는 스펙 생성 기능을 제공하는 클래스를 사용하여 간결하게 스펙을 생성할 수 있다

```java
public class OrderSpec {
    public static Specification<Order> orderer(String ordererId) {
        return (root, cb) -> cb.equal(root.get(Order_.orderer).get(Orderer_.memberId).get(MemberId_.id), ordererId);
    }

    public static Specification<Order> between(Date from , Date to) {
        return (root, cb) -> cb.between(root.get(Order_.orderDate),from, to);
    }
}
```

- 스펙 생성이 필요한 코드는 스펙 생성 기능을 제공하는 클래스를 사용하여 간결하게 표현할 수 있다

```java
Specification<Order> betweenSpec = OrderSpecs.between(fromTime, toTime);
```

### AND/ OR 스펙 조합을 위한 구현

```java
public class AndSpecification<T> implements Specification<T> {
    private List<Specification<T>> specs;
    
    public AndSpecification(Specification<T> ... specs) {
        this.specs = Arrays.asList(specs);
    }
    
    public Predicate toPredicate(Root<T> root , CriteriaBuilder cb) {
        Predicate[] predicates = specs.stream()
                .map(spec -> spec.toPredicate(root , cb))
                .toArray(size -> new Predicate[size]);
        return cb.and(predicates);
    }
}
```

```java
public class OrSpecification<T> implements Specification<T> {
    private List<Specification<T>> specs;
    
    public OrSpecification(Specification<T> ... specs) {
        this.specs = Arrays.asList(specs);
    }
    
    public Predicate toPredicate(Root<T> root , CriteriaBuilder cb) {
        Predicate[] predicates = specs.stream()
                .map(spec -> spec.toPredicate(root , cb))
                .toArray(size -> new Predicate[size]);
        return cb.and(predicates);
    }
}
```

- 두 스펙을 쉽게 생성하기 위해 Specs 클래스를 구현할 수도 있다

```java
public class Specs {
    public static<T> Specification<T> and(Specification<T> ... specs) {
        return new AndSpecification<>(specs);
    }
    
    public static <T> Specification<T> or(Specification<T> ... specs) {
        return new OrSpecification<>(specs);
    }
}
```

- 스펙을 조합해야 하는 경우

```java
Specification<Order> specs = Specs.add(OrderSpecs.orderer("madvirus"), OrderSpecs.between(fromTime, toTime));
```

### 스펙을 사용하는 JPA 리포지터리 구현

- 위에서 구현한 스펙을 사용하도록 리포지터리를 구현한다
- 리포지터리 인터페이스는 스펙을 사용하는 메서드를 제공해야 한다

```java
public interface OrderRepository {
	 public List<Order> findAll(Specification<Order> spec);
}
```

```java
public class JpaOrderRepository implements OrderRepository {
    
    private EntityManager entityManager;
    
    public List<Order> findAll(Specification<Order> spec) {
        CriteriaBuilder cb = entityManager.getCriteriaBuilder();
        CriteriaQuery<Order> criteriaQuery = cb.createQuery(Order.class);
        Root<Order> root = criteriaQuery.from(Order.class);
        Predicate predicate = spec.toPredicate(root , cb);
        criteriaQuery.where(predicate);
        criteriaQuery.orderBy(cb.desc(root.get(Order_.number).get(Orderno_.number)));
        TypeQuery<Order> query = entityManager.createQuery(criteriaQuery);
        return query.getResultList();
    }
}
```

- 검색 조건 대상이 되는 루트를 생성한다
- 루트는 Order 클래스이다
- 루트를 생성하면 파라미터로 전달받은 스펙을 사용하여 Predicate를 생성한다
- 쿼리의 조건으로 Predicate를 전달한다