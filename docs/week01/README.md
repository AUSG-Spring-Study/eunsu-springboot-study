# Week01 - 인텔리제이로 스프링 부트 시작하기

이 챕터에서는 인텔리제이를 설치하고 gradle이란 빌드 툴을 사용하는 환경에서
build.gradle 파일이 무엇이고 어떻게 외부 디펜던시를 설정하는지에 대한 내용을 다뤘다. 

## Lessons learned

- 인텔리제이는 젯브레인 툴박스라는 완전관리형 도구로 설치할 수 있다(인텔리제이 뿐만 아닌 다른 제품도 설치 가능)
- `build.gradle`
  - `build.gradle` 파일의 `ext` 란 키워드로 전역 변수를 설정할 수 있다.
  - `apply plugin: <PACKAGE_NAME>` 이란 구문으로 의존성을 적용한다고 명시할 수 있다.
  - `io.spring.dependency-management` 란 플러그인은 스프링 부트의 의존성을 관리해주는 플러그인이라 필수로 추가해야 한단다.
  - `repositories`는 의존성 라이브러리들을 어디서 가져올지 지정한다.
    - `mavenCentral`이 에전에 많이 사용되다가, 라이브러리 업로드가 어려운 문제가 있어 최근에는 `jcenter`를 사용하고 있다고 한다.
  - `dependencies`는 프로젝트 의존성을 선언하는 곳이다(node package.json의 그것과 동일한 개념이구만?)
    - 본 챕터에서는 `compile` 메소드로 의존성을 설정하고 있었으나, 최신 gradle에서는 `compile` 메소드가 deprecated 되어 `implementation` 이란 메소드를 사용하여야 했다.
      - 두 메서드의 역할에는 차이가 없다. 그러나 참조하는 의존성이 변경되었을 때 동작이 조금 다르다.[참고](https://bluayer.com/13)
  - `.gradle`, `.idea` 이건 gitignore 시키자.

## Additional knowledge

- java 애플리케이션은 JDK가 java 코드를 class로 컴파일할 때 필요한 디펜던시인 `compile classpath`와 실제로 컴파일된 코드를 실행할 때 필요한 디펜던시인 `runtime classpath` 총 2가지 유형의 classpath를 갖는다.
  - `implementation`은 디펜던시를 두 classpath에 모두 위치시킨다.
  - 그러나 디펜던시를 필요로하는 시점에 알맞은 classpath로 설정하는 것이 디펜던시 복잡도도 낮추고 더 빠른 컴파일을 달성할 수 있기 때문에 구분해주는 것이 좋다.(마치 nodejs package.json의 `dependencies`, `devDependencies`와 같이 말이다.)
  - 컴파일 타임에 필요한 디펜던시는 `compileOnly` 메서드로, 런타임에 필요한 디펜던시는 `runtimOnly` 메서드로 각 classpath에 위치시킬 수 있다.
    ![compileOnly vs implementation vs runtimeOnly](https://app.lucidchart.com/publicSegments/view/f2f7b3b9-7b90-4950-a571-b7919baff354/image.png)
  - 추가로 `test` 환경에서는 `testCompileOnly`, `testImplementation`, `testRuntimeOnly` 등의 메서드로 구분지을 수 있다.
    ![implementation methods within test configuration](https://app.lucidchart.com/publicSegments/view/2c8fba4d-c17b-423b-8f14-e82b435be395/image.png)

## References

- https://bluayer.com/13
- https://tomgregory.com/gradle-implementation-vs-compile-dependencies/
- https://effectivesquid.tistory.com/entry/Gradle-Dependency-Configuration


