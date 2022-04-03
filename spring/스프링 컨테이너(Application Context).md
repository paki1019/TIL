# ApplicationContext

- `ApplicationContext`를 스프링 컨테이너라고 함.
- `BeanFactory` 인터페이스의 하위 인터페이스, 즉 `ApplicationContext`는 `BeanFactory`에 부가기능(국제화 기능, 환경 변수 관련 처리, 애플리케이션 이벤트, 리소스 조회)을 추가한 것. BeanFactory는 스프링 컨테이너의 최상위 인터페이스로 스프링 빈을 관리하고 조회하는 역할을 함.
- `ApplicationContext`의 구현체가 여러가지 있는데, 구현체에 따라 스프링 컨테이너를 XML을 기반으로 만들 수도 있고, 자바 클래스로 만들 수도 있음. 그러나 이 둘 모두 빈 등록은 `BeanDefinition`으로 추상화해서 생성함.
- 스프링 컨테이너 내부에는 빈 저장소가 존재함. 빈 저장소는 key, value로 빈 객체를 관리함.
- 스프링 컨테이너는 기본적으로 빈을 싱글톤으로 관리해준다. 따라서 싱글톤 컨테이너라고 불리기도 함. 스프링 컨테이너가 빈을 싱글톤으로 관리해주면서 기존 싱글턴 패턴의 문제점(싱글톤 패턴 구현을 위한 코드가 추가되어야함, 구체 클래스에 의존, 유연성이 떨어짐 etc)은 없어지고, 싱글톤의 장점(매번 인스턴스를 생성할 필요없이 단 하나만 생성해서 비용을 줄일 수 있다.)만 가져갈 수 있음.

## 스프링 컨테이너 예시

```
@Configuration
public class AppConfig {

    @Bean
    public StationService stationService() {
        return new StationServiceImpl(stationRepository());
    }

    @Bean
    public StationRepository stationRepository() {
        return new MemoryStationRepository();
    }

    @Bean
    // ...

}
```

```
@SpringBootApplication
public class TestApplication {

    public static void main(String[] args) {
        SpringApplication.run(TestApplication.class, args);
    }
}
```

- `@SpringBootApplication > @SpringApplication > new AnnotationConfigApplicationContext()`를 통해 자바 기반 스프링 컨테이너가 만들어짊. 이때 만들어 둔 자바 설정 클래스(Configuration)를 파라미터로 넘겨줘서 생성. 자바 설정 클래스 내부의 @Bean 어노테이션이 붙은 메서드들을 실행하면서 빈 저장소에 실제 빈이 등록됨.
  ```
  ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
  ```
- 사용할 땐, 빈 등록 메서드 이름을 통해 객체를 가져올 수 있다.
  ```
  StationService stationService = applicationContext.getBean("stationService", StationService.class);
  ```
