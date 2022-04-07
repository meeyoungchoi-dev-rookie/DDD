- JPA 쿼리는 setFirstResult 메서드와 setMaxResults 메서드를 사용하여 페이징을 구현할 수 있다

```java
public List<Order> findByOrdererId(String ordererId, int startRow , int fetchSize) {
    TypedQuery<Order> query = entityManager.createQuery(
        "select o from Order o " + 
        "where o.orderer.memberId.id = :ordererId " + 
        "order by o.number.number desc"),Order.class);
        query.setParameter("ordererId", ordererId);
        query.setFirstResult(startRow);
        query.setMaxResults(fetchSize);
        return query.getResultList();
}
```

- setFirstResult 메서드는 읽어올 첫 번째 행 번호를 지정한다
- 첫 행은 0번부터 시작한다
- setMaxResults 메서드는 읽어올 행 개수를 지정한다

- 한 페이지에 보여줄 행 개수가 15개이고 보여줄 페이지 번호가 4라면
- 4페이지의 첫번째 행은 46번째 행이므로 시작 행 번호 값은 45가 된다
- 시작행 값과 결과 개수를 파라미터로 전달하면 4페이지에 해당하는 데이터 결과를 구할 수 있다

```java
List<Order> orders = findByOrdererId("madvirus", 45 , 15);
```

- JPQL을 사용하여 전체 애그리거트 개수 구하기

```java
@Repository
public class JpaORderRepository implements OrderRepository {
    public Long countsAll() {
        TypedQuery<Long> query = entityManager.createQUery("
        select count(o) from Order o", Long.class);
        return query.getSingleResult();
    }
}
```