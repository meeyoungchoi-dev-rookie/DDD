- 리포지터리는 애그리거트의 저장소를 표현한다
- 여러 애그리거트를 조합해서 한 화면에 보여주거나 통계 데이터를 제공하는데 적합하지 않다

`여러 애그리거트를 조합해서 한 화면에 보여주는 경우`

- JPA의 지연 로딩 , 즉시 로딩 설정 , 연관 매핑 때문에 머리가 아파진다
- 애그리거트 간 직접 연관을 맺으면 ID를 참조할때 얻을 수 있는 장점을 활용할 수 없게 된다

`통계 데이터 제공`

- 여러 테이블을 조인하거나 RDB 전용 기능을 사용해야 핟나
- JPQL이나 Criteria로 처리하기 힘들다

- 이런 기능은 조회 전용 쿼리로 처리해야 한다

## 동적 인스턴스 생성

- JPA는 쿼리 결과에서 임의의 객체를 동적으로 생성할 수 있는 기능을 제공한다
- JPQL의 select 절 뒤에 new 키워드를 사용한다
- new 키워드 뒤에 생성할 인스턴스에 클래스 이름을 지정하고 생성자에 전달하러 데이터를 지정한다
- 조회 전용 객체를 만드는 이유 : 표현 영역을 통해 사용자에게 데이터를 보여주기 위함
- 모델의 개별 프로퍼티를 생성자로 전달할수도 있다
- 주문 목록을 보여줄 목적으로 OrderView 객체를 사용한다면 생성자로 필요한 값만 전달받아도 된다

```sql
select new com.mysqhop.order.application.dto.OrderView(o.number.number, o.totalAmounts, o.orderDate, m.id.id, m.name, p.name)
```

```java
public class OrderView {
	private String number;
	private int toalAmounts;
	...
	private String productName;
	
	```
	public OrderView(String number, int totalAmounts, Date orderDate, String memberId , String memberName, String productName) {
	    this.number = number;
	    this.totalAmounts = totalAmounts;
	    ...
	    this.productName = productName;
	}
	...
	
	```

}
```

## 하이버네이트 @Subselect 사용

- 하이버네이트는 JPA 확장 기능으로 @Subselect를 제공한다
- 쿼리 결화를 @Entity로 매핑할 수 있는 유용한 기능이다
- @Immutable , @Subselect , @Synchronize는 하이버네이트 전용 어노테이션 이다
- 해당 태그를 사용하면 쿼리 결과를 @Entity로 매핑할 수 있다
- @Subselect는 조회 쿼리를 값으로 갖는다
- 하이버네이트는 select 쿼리의 결과를 매핑할 테이블처럼 사용한다
- @Subselect로 조회한 @Entity는 수정할 수 없다
- 수정하게 되면 update쿼리가 실행된다
- @Immutable 어노테이션을 사용하면 하이버네이트는 해당 엔티티의 매핑 필드가 변경되어도 DB에 반영하지 않는다
- @Synchronize - 해당 엔티티와 관련된 테이블 목록을 명시한다
- 하이버네이트는 엔티티를 로딩하기 전 지정한 테이블과 관련된 변경이 발생하면 플러시를 먼저 한다

```java
Order order = orderRepository.findById(orderNumber);
order.changeShippingInfo(newInfo);

List<OrderSummary> summaries = orderSummaryRepository.findByOrderId(userId);
```

- OrderSummary 객체에 @Synchronize 어노테이션이 붙어 있다
- @Synchronize 어노테이션으로 purchase_order  테이블을 지정하고 있다
- Order-Summar 를 로딩하기 전에 purchase_order 테이블에 변경이 발생하면 관련 내역을 플러시 한다
- 따라서 OrderSummary를 로딩하는 시점에는 변경 내역이 반영되 있는 상태이다