## ApplicationContext
  - #### 가장 많이 사용하는 인터페이스 `extends BeanFactory`
  - #### 메시지 다국화 (i18n)
  - #### 이벤트 발행 기능
  - #### 리소스 로딩 기능
----------
- ### `spring-boot-stater-web` 의존성을 사용하면 웬만한 의존성이 모두 주입된다.<br><br> <img src="https://user-images.githubusercontent.com/35948339/138130736-6e0abcfb-e03f-47e8-bb8b-e8275955a6ce.png" width=500>
-----------
## Spring Boot에서의 ComponentScan
  - #### `xml로 Bean 설정` 또는 `@Configuration + @ComponenScan`을 예전에는 사용했지만 <br><br> ✔ Spring Boot에서는 `@SpringBootApplication` 하나로 `Bean 설정` 을 할 수 있다.
  ``` java
  @SpringBootApplication
  public class DemoApplication {

      public static void main(String[] args) {

      }
  }
  ```
  - #### `@SpringBootApplication`에는 @ComponentScan, @SpringBootConfiguration이 붙어 있고,
  ``` java
    @SpringBootConfiguration
    @EnableAutoConfiguration
    @ComponentScan(
        excludeFilters = {@Filter(
        type = FilterType.CUSTOM,
        classes = {TypeExcludeFilter.class}
    ), @Filter(
        type = FilterType.CUSTOM,
        classes = {AutoConfigurationExcludeFilter.class}
    )}
    )
    public @interface SpringBootApplication {
      ...
    }
  ```
  - #### `@SpringBootConfiguration`에는 @Configuration이 붙어있는 구조로 만들어졌다.
  ``` java
    @Configuration
    public @interface SpringBootConfiguration {
      ...
    }
  ```
  

