- #### @ : 애노테이션은 Spring 3.1 부터 도입이 되었다.
``` java
  public @interface ComponentScan {
      // String을 사용하여 Type-safe하지 않음
      @AliasFor("value")
      String[] basePackages() default {};

      // Type-safe
      Class<?>[] basePackageClasses() default {};
      }
```
----------
## @ComponentScan의 주요 기능
  - ### 1️⃣ 스캔 위치 설정
      - #### 가장 중요한 설정은 `basePackage` <br><br> `basePackage`에 전달된 클래스 기준으로 @ComponentScan을 시작한다. <br><br> 즉, @ComponentScan을 가지고 있는 클래스 부터 스캔을 시작한다.
      ``` java
          // @SpringBootApplication에는 @ComponentScan이 있으므로,
          // 이 클래스 부터 컴포넌트 스캔을 시작한다
          @SpringBootApplication
          public class Demospring51Application {
              public static void main(String[] args) {
                  SpringApplication.run(Demospring51Application.class, args);
              }

          }
      ```
  ------------
  - ### 2️⃣ @Filter 
    - #### @ComponentScan은 `같은 패키지 내에 있는 Component만 스캔 할 수 있다.` <br><br> 또한, `모든 애노테이션을` 스캔 하는 것이 아니다 ‼
    ``` java
      @ComponentScan(excludeFilters = {
          @Filter(type = FilterType.CUSTOM, classes = {TypeExcludeFilter.class}), 
          @Filter(type = FilterType.CUSTOM, classes = {AutoConfigurationExcludeFilter.class})})
      public @interface SpringBootApplication {
          ...
      }
    ```
    - #### 🔼 위 @Filter를 제외한 애노테이션만 컴포넌트로 읽어온다.
-----------
## @Component (스캔에 해당 되는 애노테이션)
  - #### @Repository
  - #### @Service
  - #### @Controller
  - #### @Configuration
-------------
## Function을 사용한 Bean 등록
  - #### 리플렉션이나 CGlib, 프록시 기법을 사용하지 않아도 Bean으로 직접 등록할 수 있다.
  ``` java
    public static void main(String[] args) {
         new SpringApplicationBuilder()
         .sources(Demospring51Application.class)
         .initializers((ApplicationContextInitializer<GenericApplicationContext>)
        applicationContext -> {
         applicationContext.registerBean(MyBean.class);
         })
         .run(args);
    }
  ```
  - #### 🔼 위 방법을 @ComponentScan 대신에 사용하는 것은 비효율적이다 ❌ <br><br> Fuction 사용의 이점은 `서비스 구동 시, 로딩 시간이 빨라지는 장점`이 있다. <br><br> `@Component를 사용하여 필수 애노테이션을 등록`하고 <br><br> `부가적인 기능(Mapstruct, ModelMapper ..) 을 사용하기 위해 따로 Bean을 등록`할 때는 사용할 가치가 있다.
