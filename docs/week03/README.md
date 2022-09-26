# Week03 - 스프링 부트에서 JPA로 데이터베이스 다뤄보자

이 챕터에서는 JPA / Hibernate / Spring Data JPA의 관계를 구분하고 Spring Data JPA를 이용하여 관계형 데이터베이스를 객체지향적으로 관리하는 방법을 다뤘다.

JPA의 더티 체킹(Dirty-Checking)을 이용하여 Update 쿼리 없이 테이블 수정이 가능하단 것과 JPA Auditing을 이용해 등록/수정 시간을 자동화할 수 있단 점도 소개했다.

## Lessons learned

- iBatis(현재 MyBatis)와 같은 SQL Mapper는 개발할 때 SQL을 직접 다룸으로써 객체지향 프로그래밍을 달성하기 어려움.
  - 패러다임 불일치: RDB는 데이터를 어떻게 저장할지에 초점 vs 객체지향은 메시지를 기반으로 기능과 속성을 한 곳에서 관리하는데 초점.
- **JPA는 개발자가 객체지향적으로 프로그래밍하고, RDB에 맞게 SQL을 대신 생성**해줌으로써 위 문제를 해결.
- JPA는 인터페이스이고, 이를 구현한게 Hibernate, Eclipse Link 등.
- Spring Data JPA는 위 JPA 구현체를 좀 더 쉽게 사용하고자 추상화한것.
  - **Hibernate를 쓰는 대신 Spring Data JPA 같이 한 단계 감싸진 도구를 쓰는 이유는 '구현체 교체의 용이성' 때문**. Hibernate가 수명을 다해 다른 구현체가 떠오를 때, Spring Data JPA를 쓰고 있었다면 구현체 교체를 쉽게 할 수 있다.
  - 또한 **관계형 데이터베이스 외 다른 저장소로 쉽게 교체할 수 있는 '저장소 교체의 용이성'** 때문. MongoDB같은 걸 쓰게된다면 Spring Data JPA에서 Spring Data MongoDB로 의존성만 교체하면 된다. 
  - 이것이 가능한 이유는 Spring Data의 하위 프로젝트들이 기본적인 CRUD 인터페이스가 동일하기 때문.
- h2 데이터베이스는 인메모리 관계형 데이터베이스로 별도의 설치가 필요 없이 프로젝트 의존성으로 관리 가능하다. 메모리에서 실행되기 때문에 애플리케이션을 재시작할 때마다 초기화된다는 점을 이용하여 테스트에서 많이 쓰인다.
- `@Entity` 어노테이션이 붙은 클래스를 Entity 클래스라고도 부른다. 
  - 이 어노테이션을 붙인 클래스는 실제 테이블과 매칭되며, JPA를 사용하면서 DB 데이터를 수정할 경우 쿼리를 날리기보다 Entity 클래스의 수정을 통해 작업한다.
  - `@Id`: 테이블의 PK를 나타낸다.
  - `@Column`: 테이블의 칼럼을 나타내며 굳이 선언하지 않더라도 `@Entity` 어노테이션이 붙은 해당 클래스의 필드는 모두 컬럼이 된다. 기본값 외에 옵션 변경이 필요할 경우 사용한다.
  - `@GeneratedValue`: PK 생성 규칙을 나타낸다. 스프링부트 2.0에서는 `GenerationType.IDENTITY` 옵션을 추가해야만 auto_increment가 된다.
  - `@MappedSuperclass`: Entity 클래스가 이 어노테이션이 선언된 클래스를 상속받을경우 부모 클래스의 필드들도 컬럼으로 인식한다.
  - `@EntityListeners(AuditingEntityListener.class)`: 해당 클래스에 Auditing 기능을 포함시킨다.
  - `@CreatedDate`, `@LastModifiedDate`: Entity가 생성/수정된 시간이 자동으로 저장된다.
- 롬복 관련 어노테이션도 몇 가지 소개하면
  - `@NoArgsConstructor`: 파라미터가 없는 기본 생성자를 자동으로 추가해준다.
  - `@RequiredArgsConstructor`: `final`이 선언된 모든 필드를 인자로 하는 생성자를 추가해준다.
  - `@Getter`: 클래스의 모든 필드의 Getter 메소드를 생성해준다.
  - `@Builder`: 해당 클래스의 빌더 패턴 클래스를 생성해준다. 생성자에 선언하면 생성자에 포함된 필드만 빌더에 포함된다.
    - `@Builder` 어노테이션을 사용하면 생성자에 채워야 할 필드를 명확히 지정할 수 있단 장점이 있다. 
- **`Entity` 클래스에선 절대 Setter 메소드를 만들지 않는다.** 안그러면 해당 클래스의 인스턴스 값들이 언제 어디서 변하는지 코드상에서 명확하게 구분이 불가능하다. 대신에 값 변경이 필요하면 목적과 의도가 명확히 드러나는 메소드를 추가한다.
- `JPARepository<Entity 클래스, PK 타입>` 상속하는 interface를 만들면 해당 Entity에 대한 기본 CRUD 메소드(ex. `save()`, `findAll()`, etc.)를 자동으로 만들어준다. `@Repository` 어노테이션도 붙일 필요 없다.
  - 주의할 점은 Entity 클래스와 기본 Entity Repository는 함께 위치해야 한다. Entity 클래스는 기본 Repository 없이는 제대로 역할을 할 수가 없다.
- **스프링에선 Bean을 주입하는 3가지 방식**이 있는데, `@Autowired`, `setter`, 그리고 생성자로 주입받을 수 있다. 이 중 **생성자로 주입받는 방식이 가장 권장된다.**
- **Spring Data JPA를 사용하면 JPA의 엔티티 매니저(Entity Manager)가 기본으로 활성화**된다. 이떄 **트랜잭션 안에서 DB 데이터를 가져오면 해당 데이터는 영속성 컨텍스트가 유지**된 상태가된다. 이 상태에서 데이터의 값을 변경하면 트랜잭션이 끝나는 시점에 테이블에 변경이 반영된다. 이를 더티 체킹(Dirty Checking)이라고 한다.
- JPA Auditing 기능은 엔티티가 생성/수정된 이력을 자동으로 체크할 수 있게 해준다. 이를 활성화하려면 Application 클래스에 `@EnableJpaAuditing` 어노테이션을 추가해줘야한다.

### Test
- `@SpringBootTest` 어노테이션을 사용하면 자동으로 H2 데이터베이스를 실행해준다.
  - Week02 에서는 `WebMVCTest` 어노테이션을 사용했는데, 이 경우 JPA가 작동하지 않기 떄문에 JPA 기능을 같이 테스트하려면 `@SpringBootTest` 어노테이션을 사용해야 한다. 
- `src/main/resources/application.properties` 파일에 여러 옵션을 추가하여 설정을 적용할 수 있다.
  - `spring.jpa.show_sql=true`: 콘솔에서 SQL 쿼리를 볼 수 있다.
  - `spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect`: 출력되는 쿼리 로그를 MySQL 버전으로 볼 수 있다.
  - `spring.h2.console.enabled=true`: h2 콘솔을 띄울 수 있게 해준다.

### Architecture

![Spring Application Architecture](https://images.viblo.asia/0b2971bd-7098-4652-a677-c61aea36e032.png)

- Web Layer 
  - 흔히 사용하는 컨트롤러와 JSP/Freemarker 등의 뷰 템플릿 영역.
  - 이외에도 필터(`@Filter`), 인터셉터, 컨트롤러 어드바이스 등 **외부 요청과 응답에 대한 전반적인 영역**.
- Service Layer
  - **비지니스 로직을 처리하는 곳 아님.**
  - **트랜잭션, 도메인 간 순서 보장하는 역할.**
  - `@Service`에 사용되는 영역.
  - 일반적으로 Controller와 DAO/Repository의 중간 영역에서 사용됨.
  - `@Transaction`이 사용되어야 하는 영역이기도 함.
- Repository Layer
  - 데이터 저장소에 접근하는 영역.
- DTOs
  - 계층 간 데이터 교환을 위한 객체를 이야기하며 이들의 영역.
  - 뷰 템플릿 엔진에서 사용될 객체나 Repository Layer에서 결과로 넘겨준 객체 등이 이들을 이야기함.
- Domain Model
  - 도메인이라 불리는 개발 대상을 모든 사람이 동일한 관점에서 이해할 수 있고 공유할 수 있도록 단순화시킨 것.
  - `@Entity`가 사용된 영역 역시 도메인 모델.
  - **비즈니스 처리를 담당해야 하는 영역.**

- 비즈니스 로직을 서비스 레이어에서 처리하면 사실상 서비스 계층이 무의미함. 객체란 단순히 데이터 덩어리일 뿐임. 서비스 계층은 트랜잭션과 도메인 간의 순서만 보장해줘야 한다.



## References

- https://jojoldu.tistory.com/415
- https://itzone.com.vn/en/article/entity-domain-model-and-dto-why-so-many/