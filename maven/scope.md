# scope

- dependency 하위에 포함되는 항목
- 해당 dependency가 포함되는 범위에 대한 타입

## scope 종류

### compile

- 기본 scope. 미입력시에도 기본 적용
- 모든 상황에서 포함됨

### provided

- compile과 유사하게 모든 상황에서 수행된다
- 하지만, 다른 외부 컨테이너에서 기본 제공되는 API인경우 provided로 지정 시 마지막 패키징할 때 포함되지 않음
- 예를 들면 tomcat에서 기본적으로 servlet api를 제공하기 때문에 servlet api를 provided로 지정하면 패키징시 제외된다.

### runtime

- 컴파일 시에는 불필요 실행시에 필요한 경우.
- 런타임 및 테스트 시 classpath에 추가 되지만, 컴파일시에는 추가 되지 않음

### test

- 테스트시에만 사용

### system

- provided와 유사
- system의 특정 path를 참조하도록 지정
- Maven의 central repository를 사용하지 않음

### import

- scope는 dependencyManagement 섹션에서 pom의 의존관계에 대해 사용
