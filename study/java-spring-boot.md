## Java Spring Boot

### 개발 환경 구성하기

* [Chocolatey](https://chocolatey.org/)로 프로그램 관리하는 것이 편함

  ```
  $ choco install jdk8
  $ choco install maven
  ```
* IntelliJ에 Plugin으로 다음을 깔아 주는게 좋음
  - Lombok (소스코드 자동화)
  - Rainbow Brackets
  - Matrial Theme UI
  - Atom Material Icons
* 추가로 Ligature 폰트를 사용하면 코딩 가독성을 높일 수 있음
  - [D2Coding 글꼴](https://github.com/naver/d2codingfont)

### Lombok Annotation

* Lombok은 다음 Annotation들을 제공하여 코딩에 도움을 줌
  - @EqualAndHashCode : 비교 관련 코드 자동 생성
  - @AllArgsConstructor : 모든 필드를 포함한 생성자 자동 생성
  - @NoArgsConstructor : 파라미터가 없는 생성자 자동 생성
  - @Builder : 객체 생성후 부분 필드들을 생성할 수 있도록 코드 생성
  - @Getter / @Setter : 필드 Getter / Setter 코드 생성
  - @ToString : 객체 문자열 출력 코드 자동 생성
  - @RequiredArgsConstructor : 부분 필드 생성자 코드 생성

### Java Spring Boot의 프로그램 흐름 (절대적인것은 아님)

- Back-End
  1. Interface Package : API 부분을 담당함 (CORS 설정 필요 - @CrossOrigin 사용)
  2. Application Package : Interface가 요청하는 것을 Domain에서 가지고와 가공하여 전달
  3. Domain Package : Model와 Repository 부분을 담당함
      - Model은 자료형을 정의함
      - Repository는 DB 통신 담당으로 Model명에 Repository를 붙여서 정의함
- Front-End
  - NodeJS 기반 ReactJS 같은 것을 요즘은 많이 사용함 
  - IntelliJ에서 다른 프로젝트로 추가해서 사용함

### Database Persistence (영속화)

- Spring Boot은 JPA를 사용할 수 있음
- JPA를 추가하기 위해서는 build.gradle 다음 패키지를 추가해야 함 (H2 Memory 추가 예)
  ```gradle
  dependencies {
      implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
      implementation 'com.h2database:h2'
  }
  ```
- JPA를 위해 Annotation을 사용함
  - @Enity : Model에 붙임
  - @Id : Model이 식별자에 붙임 (Long id)
  - @GeneratedValue : Model에 식별자가 자동 생성될때 붙임
  - @Transient : Model에 정의된 변수가 DB와 관련이 없을때 붙임 
      - NULL일때 표시되지 않게 하기 위해서는 @JsonInclude(JsonInclude.Inculde.NON_NULL)을 붙임.
  - @Transactional : 하나이상의 DB작업을 취소가능 필요시 Service의 함수에 붙임.
- Repository 클래스에 다음 처럼 CrudRepository 추가하여 기본 CRUD 작업을 가능하게 함
  ```java
  public interface MenuItemRepository extends CrudRepository<MenuItem, Long> {
    List<MenuItem> findAllByRestaurantId(Long restaurantId);

    void deleteById(Long id);
  }
  ```
- DB 설정은 다음과 같이 각 프로젝트의 resources에 application.yml 파일을 수정함 
  ```yaml
  spring:
    datasource:
      url: jdbc:h2:~/data/eatgo
    jpa:
      hibernate:
        ddl-auto: update
  ```

- H2 Console
  - H2를 사용하면 프로젝트 실행 상태에서는 http://localhost:8080/h2-console을 접속하면 Console UI로 간단한 DB 작업할 수 있는 서비스를 제공함.

### Gradle에 대한 것들

* Gradle을 실행하기 위해서는 터미널에서 윈도우는 gradlew.bat로, 나머지는 gradlew로 명령함.
* 전체 빌드시 gradlew.bat build로 명령함. (빌드시 테스트도 실행함)
* 전체 테스트시 gradlew.bat test로 명령함. (IntelliJ에 Gradle로 추가할 수 있음, 명령에 여러개를 추가할 수 있음, 예: clean test)
* IntelliJ의 자체 테스트가 아닌 Gradle로 테스트하고 싶으면 Settings > Build, Execution, Deployment > Gradle에 "Build and run using"과 "Run tests using"을 Gradle로 변경하면 됨.
* IntelliJ로 패키지 분리 후 Gradle로 빌드하면 실패하는 경우가 있음.
  - 의존성이 걸리 패키지가 제대로 빌드 안되는 경우가 있기 때문에, 의존성 패키지의 build.gradle 파일에서 다음을 추가한다.

    ```gradle
    jar {
      enabled = true
    }
    ```


  - 또한, spring boot 프로젝트 의존성 패키지가 서비스로 실행되는 되는 경우가 있기 때문에 build.gradle 파일에 실행이 안되도록 다음을 추가한다.

    ```gradle
    bootJar {
      enabled = false
    }
    ```

### Profiles

- Java에는 프로젝트별 응용프로그램 속성을 설정할 수 있는데 resources 폴더에 application.properties에 정의함.
- 코드 가독성을 위해 파일 application.properties를 application.yml 이름 변경하여 YAML 형태로 정의가 가능함.
- 에를 들어 Test용 DB와 Production DB를 다르게 설정시 Profiles 기능을 사용함.
- Profiles를 설정할때는 YAML에서 기본값과 각 Profiles에 이름을 설정하여 설정함.
  ```yaml
  spring:
    datasource:
      url: jdbc:h2:~/data/eatgo
    jpa:
      hibernate:
        ddl-auto: update

  ---
  spring:
    profiles: test
    datasource:
      url: jdbc:h2:mem:test
  ```
- Gradle 명령은 다음과 같이 함
  ```
  $ SPRING_PROFILES_ACTIVE=test ./gradlew test
  ```
- IntelliJ에서는 Configurations > Gradle 에서 Environment variables에 SPRING_PROFILES_ACTIVE=test를 추가한다.