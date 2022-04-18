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


# chap2
## 아키텍처 개요
### 네개의 영역
+ 표현
+ 응용
+ 도메인 
+ 인프라스트럭처

### 계층 구조 아키텍처
+ 인프라스트럭처에 의존하면 테스트하기가 어렵다
+ 또한 기능확장의 어려움이 있다
+ 이를 해결하기 위해 DIP를 적용해야 한다


### DIP
+ 저수준 모듈이 고수준 모듈에 의존한다
+ 자신보다 변하기 쉬운것에 의존하지 않는다
+ 자신보다 변하기 쉬운 것에 의존하던 것을 추상화된 인터페이스나 상위 클래스를 두어 변하기 쉬운 것의 변화에 영향받지 않게 한다


+ 스노우타이어가 아닌 다른 타이어로 자동차가 갈아껴야 한다면
+ 자동차 클래스의 전체 코드를 변경해야 한다

![dip_적용전](https://user-images.githubusercontent.com/42866800/159007475-1cf0989a-45fc-423d-8e5f-52c22d447581.png)

+ 자동차가 의존하고 있는 타이어가 변경되어도 타이어 인터페이스를 구현한 구현체만 바꿔주면 되기 때문에 자동차 클래스는 건드리지 않아도 된다

![DIP 적용후](https://user-images.githubusercontent.com/42866800/159007516-2aad0b88-199b-4b20-8aed-fc89379ff781.png)

### 블로그 프로젝트에 DIP를 적용할 수 있는 방법
+ DAO용 mapper 인터페이스와 mapper.xml 파일에 DIP를 적용한다
+ DAO는 도메인 패키키 하위에 만들고 mapper.xml 파일의 경우 resources 폴더 밑에 인프라스트럭처 디렉토리를 만들어 파일을 생성한다
+ 서비스가 DAO에 의존하면 안된다
+ 따라서 mapper 인터페이스 구현체를 만들어 mapper 인터페이스를 상속받고 서비스단에서는 mapper 인터페이스를 DI하여 사용한다
+ 다른 인터페이스 구현체가 추가되거나 인터페이스 구현체가 변경되는 경우 서비스단 코드는 변경하지 않아도 된다

### 도메인 영역의 주요 구성 요소

#### 엔티티와 밸류
+ 도메인 모델의 엔티티는 데이터와 함께 기능을 제공하는 객체이다
+ 도메인 관점에서 기능을 구현하고 캡슐화 하여 데이터가 임의로 변경되는 것을 막는다

+ 도메인 모델의 엔티티는 두 개 이상의 데이터가 개념적으로 하나인 경우 밸류 타입을 사용하여 표현할 수 있다
+ 밸류는 불변으로 구현하는 것을 권장한다
+ 엔티티의 밸류 타입 데이터를 변경할 때 객체 자체를 완전히 교체 해야 한다

#### 애그리거트
+ 도메인 모델이 복작해 지면 개발자가 한개의 엔티티와 밸류에만 집중하게 된다
+ 큰 수준에서 모델을 이해하지 못하게 된다
+ 이를 해결하기 위해 관련 객체를 하나로 묶은 군집
+ 애그리거트는 군집에 속한 객체들을 관리하는 루트 엔티티를 갖는다

#### 리포지터리
+ 구현을 위한 도메인 모델
+ 애그리거트 단위로 도메인 객체를 저장하고 조회하는 기능을 정의한다
+ 응용 서비스는 필요한 도메인 객체를 구하거나 저장할 때 리포지터리를 사용한다
+ 응용 서비스는 트랜잭션을 관리하는데 트랜잭션 처리는 리포지터리 구현 기술에 영향을 받는다

### 요철처리 흐름
+ [요청처리 흐름](https://unique-wandflower-4cc.notion.site/cdca3ff502174cf08ee268030b6bf728)
### 인프라스트럭처 영역 개요
+ [인프라스트럭처 영역의 역할](https://unique-wandflower-4cc.notion.site/f2717298304f4163a02520a876b8d04b)

### 패키지 모듈을 구성하는 방법
+ [패키지 모듈 구성방법](https://unique-wandflower-4cc.notion.site/b147cb41ecd94a918b763f6fae32588d)

# chap2 정리
## 아키텍처 개요
+ 표현 - 사용자의 요청을 받아 응용 영역에 전달하고 응용 영역의 처리 결과를 다시 사용자에게 보여준다
+ 응용 - 시스템이 사용자에게 제공해야 할 기능을 구현한다 
- 기능을 구현하기 위해 도메인 영역의 도메인 모델을 사용한다
+ 도메인 - 도메인 모델은 도메인의 핵심 로직을 구현한다
+ 인프라스트럭처 - RDBMS 연동을 처리하고 메시징 큐에 메시지를 전송하거나 수신하는 기능을 구현한다

## DIP
- 저수준 모듈이 고수준 모듈에 의존하도록 바꾼다
- 즉 , 인터페이스를 추상화 시킨다
+ 구현 기술 교체 문제와 테스트 어려움을 해결할 수 있다
### DIP 정리

- 저수준 모듈이 고수준 모듈에 의존한다
- 자신보다 변하기 쉬운것에 의존하지 않는다
- 자신보다 변하기 쉬운 것에 의존하던 것을 추상화된 인터페이스나 상위 클래스를 두어 변하기 쉬운 것의 변화에 영향받지 않게 한다

## 도메인 영역의 주요 구성요소
+ 엔티티와 밸류
- 데이터와 함께 기능을 제공하는 객체이다
- 도메인 관점에서 기능을 구현하고 기능 구현을 캡슐롸 해서 데이터가 임의로 변경되는 것을 막는다

+ 애그리거트
- 관련 객체를 하나로 묶은 군집
+ 애그리거트가 필요한 이유
+  개별 객체뿐만 아니라 상위 수준에서 도멜을 볼 수 있어야 전체 모델의 관계와 개별 모듈을 이해하는데 도움이 된다
- 에그리거트는 군집에 속한 객체들을 관리하는 루트 엔티티를 갖는다
- 루트 엔티티는 에그리거트에 속해 있는 엔티티와 밸류 객체를 사용하여 에그리거트가 구현해야 할 기능을 제공한다
+ 리포지터리
+ 애그리거트 단위로 도메인 객체를 저장하고 조회하는 기능을 정의한다

### 요청 처리 흐름
- spring mvc를 사용하여 웹 애플리케이션을 구현했다면 컨트롤러가 사용자의 요청을 받아 처리하게 된다
- `표현영역` - 사용자가 전송한 데이터 형식이 올바른지 검사하고 문제가 없다면 데이터를 이용하여 응용 서비스세 기능 실행을 위임한다
- 표현영역은 사용자가 전송한 데이터를 응용 서비스가 요구하는 형식으로 변환하여 전달한다
- `응용 서비스` - 도메인 모델을 사용하여 기능을 구현한다
- 기능 구현에 필요한 도메인 객체를 리포지터리에서 가져와 실행한다
- 신규 도메인 객체를 생성하여 리포지터리에 저장한다

### 인프라스트럭처 개요
+ 표현 영역 , 응용 영역 , 도메인 영역을 지원한다- 무조건 인프라스트럭처에 대한 의존을 없애는 것이 좋은 것은 아니다
- 스프링을 사용할 경우 응용 서비스는 트랜잭션 처리를 위해 스프링이 제공하는 @Transactional을 사용하는 것이 편리하다
- 영속성 처리를 위해 JPA를 사용하는 경우 @Entity 나 @Table과 같은 JPA 전용 어노테이션을 도메인 모델 클래스에 사용하는 것이
- XML 매핑 설정을 사용하는 것 보다 편리하다

### 모듈 구성
+ 아키텍처의 각 영역은 별도 패키지에 위치한다
- 영역별로 모듈이 위치할 패키지를 구성할 수 있다
- 도메인이 크면 하위 도메인으로 나누고 각 하위 도메인마다 별도 패키지를 구성한다
- domain 모듈은 도메인에 속한 애그리거트를 기준으로 다시 패키지를 구성한다

# chap3. 애그리거트
## 애그리거트 필요한 이유
+ 관련된 객체를 하나의 군으로 묶어준다
- 수많은 객체를 애그리거트로 묶어서 바라보면 상위 수준에서 도메인 모델간 관계를 파악할 수 있다
- 애그리거트를 사용함으로써 모델 간 관계를 개별 모델 수준뿐만 아니라 상위 수준에서도 이해할 수 있게 된다

## 애그리거트 특징
+ 애그리거트에 속한 객체는 유사하거나 동일한 라이프사이클을 갖는다
+ 애그리거트는 경계를 갖는다
+ 도메인 규칙에 따라 함께 생성되는 구성요소는 한 애그리거트에 속할 가능성이 높다
+ 다수의 애그리거트가 한 개의 엔티티 객체만 갖는 경우가 많으며 두 개 이상의 엔티티로 구성되는 에그리거트는 드물게 존재한다

## 애그리거트 루트와 트랜잭션
- 애그리거트 루트를 통해서만 도메인 로직이 변경 가능해야 한다
- 도메인간 결합도를 낮춰야 한다
- 상세한 도메인 로직은 캡슐화 하고 외부에서 상세 도메인 로직에 접근하지 못하도록 해야 한다
- 트랜잭션은 하나의 애그리거트만 수정 가능해야 한다
- 애그리거트가 다른 애그리거트를 수정하면 안된다
- 두개 이상의 애그리거트를 변경해야 하는 경우 응용 서비스에서 각 애그리거트의 상태를 변경해야 한다

## 리포지터리

- 애그리거트 단위로 도메인 객체를 저장하고 조회하는 기능 정의
- 완전한 애그리거트를 제공하지 않으면 필드나 값이 올바르지 않아 NULLPointerException 이 발생할 수 있다
- 리포지터리는 애그리거트 루트 단위로 존재한다
- 테이블 단위로 존재하면 안된다

### ID를 이용한 참조와 조회 성능

- 참조하는 여러 애그리거트를 읽어야 할 때 조회 속도 문제가 발생할 수 있다
- 예)
- 주문 목록을 보여주기 위해 상품 애그리거트와 회원 애그리거트를 함께 읽어야 하는 경우
- 각 주문마다 상품과 회원 애그리거트를 읽어오는 경우
- 주문 개수가 10개인 경우 전체 주문을 읽어올때 한번
- 주문별로 상품을 읽어오기 위해 10번의 쿼리를 실행한다

- 조회 대상이 N일 때 N개를 읽어오는 한번의 쿼리와 연관된 데이터를 읽어오는 쿼리를 N번 실행하게 된다
- 따라서 N + 1 조회 문제가 발생한다

- ID 참조 방식을 사용하면서 N + 1 조회 문제가 발생하지 않도록 하려면
- 데이터 조회를 위한 별도 DAO를 만들고 DAO의 조회 메서드에서 세타 조인을 사용하여 한번의 쿼리로 필요한 데이터를 로딩해야 한다
- 쿼리가 복잡하거나 SQL에 특화된 기능을 사용해야 한다면 조회를 위한 부분만 MyBatis 기술을 사용할 수 있다

### 애그리거트 마다 서로 다른 저장소를 사용하는 경우

- 한번의 쿼리로 관련 애그리거트를 조회할 수 없다
- 조회 성능을 높이기 위해 캐시를 적용하거나 조회 전용 저장소를 따로 구성해야 한다
- 코드가 복잡해지는 단점이 있지만 시스템의 처리량을 높일수 있는 장점이 있다

## 애그리거트 간 1:N과 M:N 연관

- 컬렉션을 이용한 연관
- 예) 카테고리와 상품
- 한 카테고리에 한 개 이상의 상품이 속할수 있다 - 카테고리 : 상품 - 1 : N
- 한 상품이 한 카테고리에만 속할 수 있다 - 상품 : 카테고리 = N : 1

```java
public class Category {
    private Set<Product> products;
    
}
```

`Set컬렉션`

- 애그리거트 간 1 : N 관계 표현
- 1 : N 연관을 실제 구현에 반영하는 것이 요구사항을 충족하는 것과 상관없는 경우가 있다
- 예)
- 특정 카테고리에 있는 상품 목록을 보여주는 경우
- 목록 관련 요구사항은 한번에 전체 상품을 보여주기 보다는 페이징을 사요하여 제품을 나눠서 보여준다
- 단 , Product 개수가 수백에서 수만 개 정도로 많다면 이 코드를 실행할 때마다 실행 속도가 급격히 느려져 성능에 심각한 문제를 일으키게 된다

```java

public class Category {
   
   private Set<Product> products;
   
   public List<Product> getProducts(int page , int size) {
       List<Product> sortedProducts = sortById(products);
       return sotredProducts.subList((page - 1) * size, page * size);
   }
}
```

`M:N 연관`
- RDBMS를 통해 M:N 연관을 구현하면 조인을 사용한다
- JPA를 사용하면 ID 참조를 통한 M:N 단방향 연관을 구현할수 있다


## 애그리거트를 팩토리로 사용할 때 얻을 수 있는 장점

- 애그리거트가 갖고 있는 데이터를 사용하여 다른 애그리거트를 생성해야 한다면 팩토리 메서드를 구현
- 애그리거트의 도메인 로직을 팩토리 메서드로 캡슐화 한다
-------------------------------
## 리포지터리 구현

- 애그리거트를 어떤 저장소에 저장하느냐에 따라 리포지터를 구현하는 방법이 달라진다

## 모듈 위치

- 리포지터리 인터페이스는 도메인 영역에 속한다
- 리포지터리를 구현한 클래스는 인프라스트럭처 영역에 속한다
![리포지터리 구현 클래스는 인프라스트럭처 영역에 속한다](https://user-images.githubusercontent.com/42866800/160238699-000da971-23e5-4ce5-b17c-c855c5bfd23e.png)

### DIP

- 저수준 모듈이 고수준 모듈에 의존한다
- 인터페이스 또는 추상 클래스를 사용한다
- A사 혹은 B사의 Alarm이 아닌 C사의 알람을 사용하게 되더라도 Service 내부 로직을 수정하지 않아도 된다
- 왜?
- C사의 클래스에 Alarm 인터페이스를 구현시키켠 되기 때문이다

```java
public interface Alarm {
    String beep();
}
```

```java
public class A implements Alarm {
    @Override
    public String beep() {
        return "beep";
    }
}
```

```java
public class B implements Alarm {
    @Override
    public String beep() {
        return "beep";
    }
}
```

```java
public class AlarmService {
    
    private Alarm alarm;
    
    public AlarmService(Alarm alarm) {
        this.alarm = alarm;
    }
    
    public String beel() {
        return alarm.beep();
    }
}
```
-------------------------------

## 엔티티와 밸류 기본 매핑 구현

`애그리거트와 JPA 매핑을 위한 기본 규칙`

- 애그리거트 루트는 엔티티이므로 @Entity로 매핑 설정한다
- 한 테이블에 인티티와 밸류 데이터가 같이 있다면
    - 밸류는 @Enbeddable로 매핑 설정한다
    - 밸류 타입 프로퍼티는 @Embedded로 매핑 설정한다


## 기본 생성자
- @Entity와 @Embeddable로 클래스를 매핑하려면 기본 생성자가 필요하다
- JPA는 DB에서 데이터를 읽어와 매핑된 객체를 생성할 때 기본 생성자를 사용하여 객체를 생성한다
- 따라서 , Receiver 처럼 불변타입은 기본 생성자가 필요 없음에도 불구하고 기본생성자를 추가해 줘야 한다
- 기본 생성자는 JPA 프로바이더가 객체를 생성할 때만 사용한다
- 기본 생성자를 다른 코드에서 사용하면 값이 없는 온전하지 못한 객체를 만들게 되므로
- 외부에서 기본 생성자를 사용하지 못하도록 protected로 선언해야 한다

### 엔티티 와 밸류의 차이

`엔티티`

- 고유한 id를 갖는다
- 속성을 바꾸더라도 id는 바뀌지 않는다
- 속성이 바뀌더라도 여전히 같은 것으로 인식도니다
- 엔티티는 속성이 바뀔수 있기 때문에 가변 객체이다

`밸류`

- 객체의 속성이 바뀔수 없다
- 밸류의 속성을 변경하려고 하면 기존 객체는 사라지고 새로운 객체가 대신하게 된다
- 예)
- 결제시 지불한 금액이 거스름돈이 되어 돌아오지 않는다
- 낮은 금액의 새로운 돈 객체를 받는다


## 필드 접근 방식 사용

- JPA는 필드와 메서드 두 가지 방식으로 매핑을 처리할 수 있다
- 메서드 방식으로 처리하는 경우 프로퍼티를 위한 get/set 메서드를 구현해야 한다
- 엔티티에 프로퍼티를 위한 get/set 메서드를 추가하면 도메인의 의도가 사라진다
- 또한 객체가 아닌 데이터 기반으로 엔티티를 구현할 가능성이 높아진다
- set 메서드는 내부 데이터를 외부에서 변경할 수 있는 수단이 되기 때문에 캡슐화를 깨는 원인이 될 수 있다


### ID 참조와 조인 테이블을 사용한 단방향 M:N 매핑

`연관관계 매핑` - 객체의 참조와 테이블의 외래키를 매핑

왜?

상대 테이블의 PK 컬럼을 멤버변수로 갖지 않는다

엔티티 자체를 참조한다

`단방향 관계` - 두 엔티티가 관계를 맺을 때 한쪽의 엔티티만 참조

`양방향 관계` - 두 엔티티가 관계를 맺을 때 양쪽이 서로 참조

객체지향에서는 단방향 관계인지 양방향 관계인지 선택을 해야 한다

`연관관계의 주인`

- 외래키를 갖고 있는 테이블이 연관관계의 주인이 된다
- 주인이 아닌 경우 mappedBy 속성을 사용하여 주인을 정해 준다
- 주인인 경우 @JoinColumn을 사용하여 매핑할 외래 키 이름을 지정한다

### 블로그 프로젝트에 적용할 수 있는 방법

- 객체의 협력 관계를 분석하여 JPA를 적용한다
- 관계에서 주인을 판별할때 mappedBy 속성을 사용하여 주인을 지정해 준다

## 애그리거트에 속한 객체가 모두 모여야 완전한 하나가 된다

- 애그리거트 루트를 로딩하면 루트에 속한 모든 객체가 완전한 상태여야 한다
- 조회시 Product 애그리거트는 완전히 하나가 되어야 한다
- 애그리거트에 속한 객체가 조회 결과인 Product 애그리거트에 모두 있어야 한다

```java
Product product = productRepository.findById(id);
```

### 애그리거트를 완전한 상태로 만드는 방법

- 애그리거트 루트에서 조회 방식을 즉시 로딩으로 설정
- 컬렉션이나 @Entity 에 대한 매핑의 fetch 설정을 FetchType.EAGER로 설정
- 애그리거트 루트를 조회할 때 연관된 애그리거트 구성요소를 DB에서 함께 읽어온다

### 즉시 로딩의 단점

```java
@Entity
@Table(name = "product")
public class Product {
    
    
    @OneToMany(
            cascade = {CascadeType.PERSIST, CascadeTpe.REMOVE},
            orphanRevomal = true,
            fetch = FetchType.EAGER)
    @JoinColumn(name = "product_id")
    private List<Image> images = new ArrayList<>();
    
    @ElementCollection(fetch = FetchType.EAGER)
    @CollectionTable(name = "product_option",
             joinColumns = @JoinColumn(name = "product_id"))
    private List<Options> options = new ArrayList<>();
    
    ...
}
```

- find 메서드로 Product를 조회하면 하이버네이트는 Product , Image , Option 테이블을 조인한 쿼리를 실행한다

```sql
select
			....
from product p
		 left outer join image img on p.product_id = img.product_id
     left outer join product_option opt on p.product_id = opt.product_id
where p.product_id = ?

```

- 카타시안 조인을 사용하게 되면 쿼리 결과에 중복이 발생되게 된다
- 예)
- Product가 갖고있는 Image가 2개이고 option이 2개인 경우 카타시안 조인을 했을때 4개의 행이 구해진다
- product 테이블은 4번 중복되고 image와 product_option은 2번 중복된다
- 조회의 성능이 나빠지는 문제가 발생된다

## 애그리거트가 완전한 상태여야 한다

- 애그리거트를 저장하거나 삭제할 때도 하나로 처리해야 한다
- 저장 메서드는 애그리거트에 속한 모든 객체를 저장해야 한다
- 삭제 메서드는 애그리거트에 속한 모든 객체를 삭제해야 한다
- @Entity 타입에 대한 매핑은 cascade 속성을 사용하여 저장과 삭제시 함께 처리되도록 해야 한다

```java
@OneToMany(cascade = {CascadeType.PERSIST, CascadeType.REMOVE},
    orphanRemoval = true)
@JoinColumn(name = "product_id")
@OrderColumn(name = "list_idx")
private List<Options> options = new ArrayList<>();
```

- `영속성` - 엔티티를 영구적으로 저장해주는 환경
- `영속성 컨텍스트` - EntityManager를 통해 Entity를 저장하거나 조회할때 EntityManager는 영속성 컨텍스트에 Entity를 보관하고 관리한다

```java
EntityManger.persist(Entity 객체);
```

- 영속성 컨텍스트는 EntityManager를 통해 접근할 수 있고 관리된다
- 영속성 컨텍스트는 Entity를 식별자로 구분한다
- JPA는 트랜잭션을 커밋하는 순간 영속성 컨텍스트에 새로 저장된 Entity를 데이터베이스에 반영한다
- 영속성 컨텍스트는 객체의 동일성을 보장한다
- 트랜잭션을 지원하는 쓰기 지연을 수행한다

### Entity의 4가지 상태

- 비영속 - 영속성 컨텍스트와 전혀 관계가 없는 상태 (객체 생성)
- 영속 - 영속성 컨텍스트에 저장된 상태
    - 객체 생성후 EntityManager를 통해 영속성 컨텍스트에 저장
- 준영속
    - 영속성 컨텍스트에 저장되었다가 분리된 상태
    - 더이상 영속성 컨텍스트가 엔티티를 관리하지 않는다
- 삭제
    - 삭제된 상태
    - 영속성 컨텍스트와 데이터베이스에서 Entity 삭제

### flush()

- 영속성 컨텍스트의 변경 내용을 데이터베이스에 반영한다
- 변경 감지 동작
- 영속성 컨텍스트에 있는 모든 Entity를 스냅샷과 비교하여 수정된 Entity를 찾는다
- 수정된 Entity는 수정 쿼리를 만들어 쓰기 지연 SQL 저장소에 저장한다
    - 등록 , 수정 , 삭제 퀄
- 쓰기 지연 SQL 저장소의 쿼리를 데이터베이스에 전송한다

### 쓰기 지연
- 영속성 컨텍스트에 변경이 발생했을때 바로 데이터베이스로 쿼리를 보내지 않고
- SQL 쿼리를 버퍼에 모아뒀다가 영속성 컨텍스트가 flush 하는 시점에 쿼리를 데이터베이스로 보낸다

## 식별자를 생성하는 방법
- 사용자가 직접 생성
- 도메인 로직으로 생성
- DB를 통해 일련변호 사용

---------------------------------
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

### 스펙 조합

- 스펙의 장점은 조합에 있다
- 두 스펙을 AND 연산이나 OR 연산자로 조합하여 새로운 스펙을 만들 수 있다
- 조합한 스펙을 다시 조합하여 더 복잡한 스펙을 만들 수 있다


### 리포지터에서 모든 애그리거트를 조회하는 경우 문제점
- 리포지터리에서 모든 애그리거트를 조회한 후 스펙을 사용해서 걸러내면
- 실행 속도 문제가 생긴다
- 예)
- 애그리거트가 10만 개인 경우 10만 개 데이터를 DB에서 메모리로 로딩한 후
- 다시 10만개 객체를 루프를 돌면서 스펙을 검사해야 한다
- 시스템 성능을 느리게 만든다

### 해결책

- 쿼리에 where 조건을 붙여 필요한 데이터만 걸러내야 한다
- 즉 메모리에서 걸래내는 방식에서 쿼리의 where를 사용하는 방식으로 변경해야 한다
- CreaterialBuilder와 Predicate를 사용하여 검색조건을 구할 수 있다


## 정렬 순서

- JPA의 CriteriaQuery 객체의 orderBy 메서드를 사용하여 정렬 순서를 지정한다
- CriteriaBuilder 객체의 asc 메서드와 desc 메서드로 정렬할 대상을 지정한다
- JPQL을 사용하는 경우 order by 절을 사용하면 된다

```java
TypedQuery<Order> query = entityManager.createQuery("
        select o from Order o" + "where o.orderer.memberId.id = :ordererId " + 
        "order by o.number.number desc",
        Order.class
);
```

- JPA Criteria는 Order 타입을 사용해서 정렬 순서를 지정한다
- Order 객체는 CriteriaBuilder 객체를 통해 생성할 수 있다
- 정렬순서를 지정하는 코드는 리포지터리를 사용하는 응용서비스에 위치하게 된다
- 응용서비스는 CriteriaBuilder 객체에 접근할 수 없다
- 응용서비스에서는 다른 타입을 사용하여 리포지터리에 정렬 순서를 전달하고 JPA 리포지터리를 다시 JPA Order로 변환해 줘야 한다

```java
List<Order> orders = orderRepository.findAll(someSpec, "number.number desc");
```

## 페이징 개수 구하기
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

## 조회 전용 기능 구현
### 동적 인스턴스 생성

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


## 하이버네이트 @SubSelect
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
------------------------------------

# 표현 영역과 응용 영역
## 개요

- 도메인이 제 기능을 수행하려면 사용자와 도메인을 연결해 주는 매개체가 필요하다
- 응용 영역과 표현 영역이 사용자와 도메인을 연결해 주는 매개체 역할을 한다
- 표현영역
- 사용자의 요청을 해석한다
- 요청을 받은 표현 영역은 URL , 요청 파라미터 , 쿠키 , 헤더 등을 사용하여 사용자가 어떤 기능을 실행하고 싶어 하는 지 판별하고 기능을 제공하는 응용 서비스를 실행한다


## 응용 서비스의 역할

- 응용 서비스는 사용자가 요청한 기능을 실행한다
- 사용자의 요청을 처리하기 위해 리포지터리로부터 도메인 객체를 구하고
- 도메인 객체를 사용한다
- 응용 서비스의 주요 역할은 도메인 객체를 사용하여 자용자의 요청을 처리한다
- 사용자 입장에서 보았을때 응용 서비스는 돔인 영역과 표현 영역을 연결해 주는 파시드 역할을 한다


## 도메인 로직 넣지 않기

- 도메인 로직은 도메인 영역에 위치하고 응용 서비스는 도메인 로직을 구현하지 않는다
- 도메인 로직을 도메인 영역과 응용 서비스에 분산해서 구현하면 코드 품질에 문제가 발생한다
- 코드의 응집성이 떨어진다


## 응용 서비스의 인터페이스와 클래스
- 인터페이스가 필요한 경우
- 구현 클래스가 여러개인 경우
- 구현 클래스가 다수 존재하거나 런타임에 구현 객체를 교체해야 할 경우 인터페이스를 유용하게 사용할 수 있다
- 응용 서비스는 런타임에 구현 클래스를 교체하는 경우가 드물다
- 한 응용 서비스의 구현 클래스가 두개인 경우도 드물다
- 인터페이스와 클래스를 따로 구현하면 소스 파일만 많아지고 구현 클래스에 대한 간접 참조가 증하하여 전체적인 구조가 복잡해 진다

## 메서드 파라미터와 값 리턴
- 응용 서비스가 제공하는 메서드는 도메인을 사용하여 사용자가 요구한 기능을 실행하는데 필요한 값을 파라미터를 통해 전달받아야 한다
- 필요한 각 값을 개별 파라미터로 전달받을 수도 있고
- 값 전달을 위한 별도 데이터 클래스를 만들어 전달받을 수도 있다
- 응용 서비스에 데이터로 전달할 요청 파라미터가 두 개 이상 존재하면 데이터 전달을 위한 별도 클래스를 사용하는 것이 바람직하다

### 결론

- 사용자와 도메인을 연결해주는 매개체가 표현영역과 응용영역 이다
- 응용 서비스가 도메인 로직을 구현하면 코드 품질에 않좋다
- 도메인 데이터와 데이터를 조작하는 도메인 로직이 한 영역에 위치하지 않고 서로 다른 영역에 위치하면 도메인 로직을 파악하기 위해 여려 영역을 분석해야 한다
- 도메인 과 관련된 기능별로 서비스 클래스를 구분하여 구현한다
- 각 클래스 별로 필요한 의존객체만 포함하므로 다른 기능을 구현한 코드에 영향을 주지 않는다


## 표현영역

- 사용자가 시스템을 사용할 수 있는 화면을 제공하고 제어한다
- 사용자의 요청을 알맞은 응용 서비스에 전달하고 결과를 사용자에게 제공한다
    - 화면을 보여주는데 필요한 데이터를 읽거나 도메인의 상태를 변경해야 할 떄 응용 서비스를 사용한다
    - 이 과정에서 표현영역은 사용자의 요청 데이터를 응용 서비스가 요구하는 형식으로 변환하고 응용 서비스의 결과를 사용자에게 응답할 수 있는 형식으로 변환한다
- 사용자의 세션을 관리한다
    - 사용자의 연결상태인 세션을 관리한다
    - 웹의 경우 쿠키나 서버 세션을 사용하여 사용자의 연결 상태를 관리한다
    - 세션 관리는 권한 검사와도 연결된다


## 값 검증

- 표현 영역과 응용 서비스 두 곳에서 모두 수행할 수 있다
- 표현영역에서 검증하는 방법

```java
@GetMapping
public String join(JoinRequest joinRequest, Errors errors) {
    checkEmpty(joinRequest.getId(), "id", errors);
    checkEmpty(joinRequest.getName(), "name", errors);
    ...
    
    
    if (errors.hashErrors()) {
        return formView;
    }
    
    try {
        joinService.join(joinRequest);
        return successView;
    } catch (DuplicationException ex) {
        errors.rejectValue(ex.getPropertyName(), "duplicate");
        return formView;
    }
}

private void checkEmpty(String value, String property, Errors errors) {
    if (isEmpty(value)) {
        errors.rejectValue(property, "empty");
    }
}
```

- 응용 서비스에서 검증하는 방법

```java
public class JoinService {
    public void join(JoinRequest request, Errors errors) {
        new JoinRequestValidator().validate(joinRequest, errors);
        if (!errors.hasErrors()) checkDuplicateId(joinReq.getId(), errors)
        if (errors.hasErrors()) return;
    }
}
```

## 권한 검사

- 개발할 시스템 마다 권한의 복잡도가 달라진다
- 단순한 시스템은 인증 여부만 검사하면 되는데 , 어떤 시스템은 관리자인지 여부에 따라 사용할 수 있는 기능이 달라지기도 한다
- 표현영역 , 응용 서비스 영역 , 도메인 영역에서 권한 검사를 수행할 수 있다
- 서블릿 필터를 통해 사용자의 인증 정보를 생성하고 인증 여부를 검사한다
- 스프링 시큐리티는 필터를 사용해서 인증 정보를 생성하고 웹 접근을 제어하는 기능을 제공한다
- AOP를 활용하여 애노테이션으로 서비스 메서드에 대한 권한검사를 할 수 있는 기능을 제공한다


# 도메인 서비스

- 한 애그리거트에 넣기 애매한 도메인 개념을 구현하려면 도메인 서비스를 사용하여 도메인 개념을 명시적으로 드러내면 된다
- 도메인 서비스는 도메인 로직을 다룬다
- 도메인 영역의 애그리거트나 밸류와 같은 차이점은 상태 없이 로직만 구현한다
- 도메인 서비스를 구현하는데 필요한 상태는 애그리거트를 통해 전달받는다

## 도메인 서비스 객체를 애그리거트에 주입하지 않기

```java
public class Order {
	@Autowired
  private DiscountCalcualtionService discountCalculationService;

  ...
}
```

- 애그리거트의 메서드를 실행할때 도메인 서비스 객체를 파라미터로 전달한다는 것은 애그리거트가 도메인 서비스에 의존한다는 것을 의미한다
- 도메인 객체는 필드로 구성된 데이터와 메서드를 사용하여 하나의 모델을 표현한다
- 모델의 데이터를 담는 필드는 모델에서 중요한 구성요소이다
- 그런데 discountService 필드는 데이터 자체와는 관련이 없다
- Order 객체를 DB에 보관할때 다른 필드와는 달리 저장 대상이 아니다
- 또 Order가 제공하는 모든 기능에서 discountCalculationService를 필요로 하지 않는다
- 일부 기능을 위해 도메인 서비스 객체를 애그리거트에 의존 주입할 필요는 없다

### 도메인 서비스의 인터페이스와 클래스

- 도메인 서비스의 로직이 고정되지 않은 경우 도메인 서비스 자체를 인터페이스로 구현하고 이를 구현한 클래스를 둘 수도 있다
- 도메인 로직을 외부 시스템이나 별도 엔진을 사용해서 구현해야 할 경우 인터페이스와 클래스를 분리하면 된다
- 도메인 서비스의 구현이 특정 구현 기술에 의존하거나 외부 API를 통해 실행된다면 도메인 영역의 도메인 서비스는 인터페이스로 추상화 해야 한다

![도메인 서비스의 구현이 특정 기술에 종속되면 인터페이스와 구현 클래스로 분리한다](https://user-images.githubusercontent.com/42866800/163554539-57737f63-a623-4ac1-8887-a008406151dd.png)

# 애그리거트와 트랜잭션
## 선점 잠금

- 애그리거트를 구한 스레드가 애그리거트 사용이 끝날때 까지 다른 스레드가 해당 애그리거트를 수정하는 것을 막는다
- 한 스레드가 애그리거트를 수정하는 동안 다른 스레드가 수정할 수 없다
- 동시에 애그리거트를 수정할 때 발생하는 데이터 충돌 문제를 해소 할 수 있다
- 선점 잠금은 DBMS가 제공하는 행 단위 잠금을 사용해서 구현한다
- 다수의 DBMS가 for update 쿼리를 사용해서 특정 레코드에 한 사용자만 접근할 수 있는 잠금 장치를 제공한다

## 비선점 잠금

- 잠금을 해서 동시에 접근하는 것을 막는 대신 변경한 데이터를 실제 DBMS에 반영하는 시점에 변경 가능 여부를 확인한다
- 기능을 실행하는 과정에서 애그리거트 데이터가 변경되면 JPA는 트랜잭션 종료 시점에 비선점 잠금을 위한 쿼리를 실행한다
- 비선점 잠금을 위한 쿼리를 실행할 때 쿼리 실행 결과로 수정된 행의 개수가 0이면 이미 누군가 앞서 데이터를 수정한 것이다
- 이는 트랜잭션이 충돌한 것이므로 태랜잭션 종료 시점에 익셉션이 발생한다
- 애그리거트를 수정할 때 사용자가 전송한 버전과 애그리거트 버전이 동일한 경우 수정 기능을 수행하도록 하여 트랜잭션 충돌 문제를 해소한다


### 강제 버전 증가

- 연관된 엔티티의 값이 변경되어도 루트 엔티티 자체의 값은 증가되지 않는다
- 루트 엔티티의 버전 값을 갱신하지 않는다
- 하지만 비선점 잠금이 올바르게 동작하려면 애그리거트 내 어떤 구성요소의 상태가 변하면 루트 애그리거트의 버전 값을 증가시켜야 한다

### 오프라인 선점 잠금

- 여러 트랜잭션에 걸쳐 동시 변경을 막는다
- 첫번째 트랜잭션을 시작할 때 오프라인 잠금을 선점한다
- 마지막 트랜잭션에서 잠금을 해제한다
- 잠금을 해제하기 전까지 다른 사용자는 잠금을 구할 수 없다
- 오프라인 선점 방식은 잠금의 유효 시간을 가져야 한다
- 유효 시간이 지나면 자동으로 잠금을 해제해서 다른 사용자가 잠금을 일정 시간 후에 다시 구 할 수 있도록 해야 한다
- 오프라인 선점 잠금은 잠금 선점 시도 , 잠금 확인 , 잠금 해제 , 락 유효 시간 연장의 네가지 기능을 제공한다
- LockManager 인터페이스를 통해 해당 기능을 실행할 수 있다

- DB를 사용한 LockManager 구현
- MySQL을 사용하는 경우 잠금 정보를 저장할 테이블과 인덱스를 생성한다