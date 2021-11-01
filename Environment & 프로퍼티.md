## 프로퍼티
  - #### 다양한 방법으로 정의할 수 있는 설정 값
  - #### Environment의 역할은 `프로퍼티 소스 설정` 및 `프로퍼티 값 가져오기`
  - ### 프로퍼티에는 우선 순위가 있다.
    - #### StandardServletEnvironment의 우선순위
      - #### ServletConfig 매개변수
      - #### ServletContext 매개변수
      - #### JNDI (java:comp/env/)
      - #### JVM 시스템 프로퍼티 `(-Dkey="value")` <br><br> ![image](https://user-images.githubusercontent.com/35948339/139706180-8d1e60a6-e96a-42cc-ac12-0c90389c1cfa.png)
      ```java
        @Component
        public class AppRunner implements ApplicationRunner {
            @Autowired
            ApplicationContext ctx;

            @Autowired
            BookRepository bookRepository;

            @Override
            public void run(ApplicationArguments args) throws Exception {
                Environment environment = ctx.getEnvironment();
                System.out.println(environment.getProperty("app.name"));
            }
        }
      ```
      ```
        spring5
      ```
      - #### JVM 시스템 환경 변수 `(운영 체제 환경 변수)`
  - #### @PropertySource
    - #### Enviroment를 통해 프로퍼티를 추가하는 방법
  - #### 스프링 부트의 외부 설정 참고
    - #### 기본 프로퍼티 소스 지원 `(application.properties)`
    ```
      // application.properties
      abb.about=spring
    ```
    ``` java
      @SpringBootApplication
      @PropertySource("classpath:/application.properties")
      public class Demospring51Application {
          public static void main(String[] args) {
              SpringApplication.run(Demospring51Application.class, args);
          }

      }
    ```
    - #### @PropertySource로 classpath를 정해주면 된다.
    ``` java
      @Override
      public void run(ApplicationArguments args) throws Exception {
          Environment environment = ctx.getEnvironment();
          
          // application.properties에 선언한 이름 호출
          System.out.println(environment.getProperty("app.about"));
      }
    ```
    ```
      spring
    ```
    - #### 프로파일까지 고려한 계층형 프로퍼티 우선 순위 제공
      - #### `VM Options에 -Dapp의 우선순위` 가 @PropertySource`보다 높다.`
    - #### 추가로 이런 방법도 있다.
    ``` java
      @Value("${app.name}")
      String appName;
      
      System.out.println(appName);
    ```
    ```
      spring5
    ```
