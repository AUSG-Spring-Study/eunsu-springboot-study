# Week02 - 스프링 부트에서 테스트 코드를 작성하자

이 챕터에서는 간단한 Controller(`HelloController`)와 Dto(`HelloResponseDto`)를 만들고 각각에 대한 테스트코드를 작성하는 내용을 다뤘다.
JUnit과 springboot 프레임워크에서 제공하는 테스트 유틸리티들을 직접 다룰 수 있었다.

## Lessons learned

- 여기서 `Application`이란 메인 클래스를 생성하였는데, 여기에 달린 `@SpringBootApplication` 어노테이션이 스프링 부트의 자동 설정, 스프링 Bean 읽기와 생성을 모두 자동으로 설정해준다고 한다.
  - `@SpringBootApplication` 이 있는 위치부터 설정을 읽어나가기 때문에 이 **메인 클래스는 항상 프로젝트 최상단에 위치해야 한다**.
  - `main` 메소드에서 실행하는 `SpringApplication.run` 메서드는 내장 WAS를 띄운다고 한다(디폴트로 Tomcat). 이렇게 하면 외부에 별도로 WAS를 둘 필요가 없고, 스프링 부트로 만들어진 Jar 파일만 실행하면 된다.
    - 스프링 부트에서는 **내장 WAS를 사용하는 것을 권장**하는데, 그 이유는 언제 어디서나 **같은 환경에서 스프링 부트를 배포**할 수 있기 때문이다.
- `@RestController` 어노테이션은 클래스를 JSON을 반환하는 컨트롤러로 만들어준다. 
- `@GetMapping` 어노테이션은 클래스 메서드를 HTTP GET요청을 받을 수 있는 API로 만들어준다.(아마 HTTP verb 종류별로 다 있는 듯?)
- 롬복(Lombok)은 자주 사용되는 코드(Getter, Setter, Constructor, etc.)를 어노테이션으로 자동으로 생성해주는 라이브러리이다. 쓰면 편하다.
  - `@Getter` - 클래스에 선언하면 해당 클래스 필드의 get 메소드를 생성해준다.
  - `@RequiredArgsConstructor` - 클래스에 선언하면 해당 클래스에 선언된 모든 `final` 필드가 포함된 생성자를 만들어준다.

### Test

- Junit의 `@RunWith(SpringRunner.class)` 어노테이션은 테스트를 실행할 때 JUnit에 내장된 실행자 외에 다른 실행자를 실행시킨다.(테스트 러너를 결정하는 느낌. 마치 node.js에서 테스트 러너로 jest를 쓸지 vitest를 쓸지 고를 수 있게 해주는 느낌?)
  - 여기서는 `SpringRunner` 라는 것을 사용했다. 만약 파라미터로 지정하지 않으면 JUnit의 built-in runner를 사용하는 것 같다.
- `@WebMvcTest` 어노테이션은 여러 스프링 어노테이션 중 Web(Spring MVC)에 집중할 수 있는 어노테이션으로, 선언할 경우 `@Controller`, `@ControllerAdvice` 등을 사용할 수 있다고 한다.
  - 단 `@Service`, `@Component`, `@Repository` 등은 사용할 수 없다고 한다.
- `@Autowired`는 스프링이 관리하는 Bean을 자동으로 주입받게 해주는 어노테이션이다.
- `HelloControllerTest`에 필드로 `private MockMvc mvc`를 선언했는데, 웹 API를 테스트할 떄 사용하는 클래스로 이를 통해 HTTP GET등에 대한 API를 테스트할 수 있게 된다(ex. `mvc.perform(get("/hello"))`).
- `Junit`에서 제공하는 단언 유틸리티 대신 `assertj` 라는 단언 라이브러리가 제공하는 메서드(`assertThat`)을 사용하면 IDE 자동완성, CoreMatcher 불필요 등 여러 이점이 있다.

## Additional knowledge

- DTO는 Data Transfer Object의 약자로 계층 간 데이터 전송을 위해 도메인 모델 대신 사용되는 객체이다.
![Dta between layers](https://hudi.blog/static/9a11ff7dc9390be191f60fac775079fb/9d567/dto.png)
- DTO를 사용하는 이유, 즉 이를 사용해서 누릴 수 있는 이점은 다음과 같다.
  - 데이터를 전송하기 위한 직렬화 메커니즘을 캡슐화 할 수 있다(다른 layer에 직렬화 로직이 들어가지 않도록 방지)
  - 여러 데이터를 따로 전달하는 것 보다 데이터를 DTO로 묶어서 보내면 검증과 로직 처리를 한번에 할 수 있음
  - 안정성과 수행시간 모두 DTO로 묶어 보내는 것이 유리
- 일반적으로 DTO는 요청 <-> 컨트롤러 사이에서 많이 사용된다.


## Appendix

- 책에서는 테스트 코드 실행하다 에러나면 Gradle 버전이 5인지 확인하라고 하는데, 현 시점 기준(2022.09.11) 최신 Gradle 버전이 7.5.1 이라 버전을 낮추기 싫어서 다른 방법을 찾아봤다. 다행히 책에서 나온 lombok 라이브러리 레지스트리(`org.projectlombok:lombok`) 대신 gradle plugin을 통해 설치(`io.freefair.lombok`)하니 잘 동작했다.

## References

- https://hudi.blog/data-transfer-object/
- https://kafcamus.tistory.com/13
- https://projectlombok.org/setup/gradle