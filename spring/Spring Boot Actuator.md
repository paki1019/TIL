# Spring Boot Actuator

어플리케이션을 모니터링하고 관리하는 기능을 Spring Boot에서 자체적으로 제공하는 것. http와 JMX를 통해 확인할 수 있음.

# 의존성(Dependency)

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>

dependencies {
    compile("org.springframework.boot:spring-boot-starter-actuator")
}
```

# 엔드 포인트 종류 (EndPoints List)

[Spring Boot Actuator: EndPoints](https://docs.spring.io/spring-boot/docs/2.1.8.RELEASE/reference/html/production-ready-endpoints.html)

# configuration

actuator에서 제공하는 List는 민감한 정보들이 포함되어있음. 이는 보안상 위험요소가 될 수 있으므로 전 기능을 사용하기 위해서는 설정정보들을 잡아주어야 함.

```
# application.yml
# 전체 노출
management:
  endpoints:
    web:
      exposure:
        include: "*"

# info, health, httptrace만 노출
management:
  endpoints:
    web:
      exposure:
        include:
          - "info"
          - "health"
          - "httptrace"
```
