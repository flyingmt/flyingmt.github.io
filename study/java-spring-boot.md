## Java Spring Boot

### Gradle에 대한 것들

* 윈도우는 gradlew.bat로, 나머지는 gradlew로 명령함.
* 전체 빌드시 gradlew.bat build로 명령함. (빌드시 테스트도 실행함)
* 전체 테스트시 gradlew.bat test로 명령함. (IntelliJ에 Gradle로 추가할 수 있음, 명령에 여러개를 추가할 수 있음, 예: clean test)
* IntelliJ의 자체 테스트가 아닌 Gradle로 테스트하고 싶으면 Settings > Build, Execution, Deployment > Gradle에 "Build and run using"과 "Run tests using"을 Gradle로 변경하면 됨.
* IntelliJ로 패키지 분리 후 Gradle로 빌드하면 실패하는 경우가 있음.
  - 의존성이 걸리 패키지가 제대로 빌드 안되는 경우가 있기 때문에, 의존성 패키지의 build.gradle 파일에서 다음을 추가한다.
<code>
jar {
	enabled = true
}
</code>
  - 또한, spring boot 프로젝트 의존성 패키지가 서비스로 실행되는 되는 경우가 있기 때문에 build.gradle 파일에 실행이 안되도록 다음을 추가한다.
<code>
bootJar {
	enabled = false
}
</code>
