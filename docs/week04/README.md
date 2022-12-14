# Week04 - 머스테치로 화면 구성하기

이번 챕터에서는 머스테치를 활용하여 스프링 부트에서 HTML을 구성하는 법과 머스테치로 구성된 화면을 컨트롤러로 반환하는 내용을 다뤘다.

템플릿 엔진 답게 머스테치에 repository로부터 가져온 데이터를 Dto에 담아 HTML에 데이터를 바인딩 하는 방법도 소개했다. 개인적으로 Vue의 문법과 유사하다고 생각해서 이해가 쉬웠다.

이번 챕터에서는 HTML, JS를 작성하는 내용이 많았어서 새롭게 알게된 내용이 그리 많지는 않았다. 

## Lessons learned

- 머스테치는 HTML을 만들어주는 템플릿 엔진이다.
  - 문법이 심플하고, 로직 코드를 사용할 수 없어 View와 서버의 역할이 명확히 분리되며 Mustache.js와 Mustache.java 2가지가 있어 하나의 문법으로 클라이언트/서버 템플릿을 모두 사용 가능하단 장점이 있다.
  - 스프링 부트에서 공식적으로 지원한다.
- 머스테치 파일 위치는 기본적으로 `src/main/resources/templates` 이고, 여기에 `index.mustache` 등 파일을 생성하면 스프링 부트에서 자동으로 로딩한다.
- 머스테치 스타터를 사용하면 컨트롤러에서 문자열을 반환할 때 앞의 경로와 뒤의 파일 확장자는 자동으로 지정되어 머스테치 파일을 반환하도록 해준다.
- 머스테치 화면이 제대로 나오는지 테스트하기 위해 `TestRestTemplate`를 활용할 수 있다.
- JPA repository 인터페이스에 원하는 쿼리를 추가할 수 있는데 이떄 `@Query` 어노테이션을 사용해서 정의할 수 있다.
  - 규모가 있는 프로젝트에서는 Entity만으로 쿼리를 처리하기 어려워 조회용 프레임워크를 사용하곤 한다. 대표적으로 QueryDsl이 있다.
- `@Transactional` 어노테이션에 `readOnly=true` 옵션을 주면 트랜잭션 범위는 유지하되, 조회 기능만 남겨두어 조회 속도가 개선된다. CUD가 없는 서비스 메소드에 사용하는 걸 추천.
