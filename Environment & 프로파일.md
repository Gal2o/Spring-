## Environment
  - #### 프로파일과 프로퍼티를 다루는 인터페이스
  ``` java
    public interface ApplicationContext extends EnvironmentCapable ... {
    }
  ```
  - #### `getEnvironment()`
-------
## 프로파일
  - #### `EnvironmentCapable이 제공`하는 기능 중 하나
  - #### `Bean 들의 그룹`
  - #### `Enviroment의 역할`은 `활성화 할 프로파일 확인 및 설정`
  - ### 프로파일 정의 하기
    - ### 클래스에 정의하기
      ``` java
        @Component
        public class AppRunner implements ApplicationRunner {
            @Autowired
            ApplicationContext ctx;

            @Autowired
            BookRepository bookRepository;

            @Override
            public void run(ApplicationArguments args) throws Exception {
                Environment environment = ctx.getEnvironment();
                System.out.println(Arrays.toString(environment.getActiveProfiles()));
                System.out.println(Arrays.toString(environment.getDefaultProfiles()));
            }
        }
      ```
      - #### Configuration 클래스를 만들어서 @Configuration + @Profile
      ``` java
         @Configuration
         @Profile("test")
         public class TestConfiguration {
            @Bean
            public BookRepository bookRepository() {
                return new TestBookRepository();
            }
         }
      ```
      ```
        [test]
        [default]
      ```
      ------------
      - #### BookRepository 인터페이스를 implements 하는 클래스에서 @Component + @Profile
      ``` java
        @Repository
        @Profile("test")
        public class TestBookRepository implements BookRepository{
        }
      ```
      ```
        [test]
        [default]
      ```
    -------------
    - ### 메소드에 정의하기
      ``` java
         @Configuration
         public class TestConfiguration {
            @Bean
            @Profile("test")
            public BookRepository bookRepository() {
                return new TestBookRepository();
            }
         }
      ```
      - #### 이 방법은 번거롭기 떄문에 선호하지 않는다.
    -------------
    - #### 프로파일 설정 하는 방법
      - #### 두 가지 방법 중 하나로 test 프로파일을 설정해줘야 한다.
      - #### ![image](https://user-images.githubusercontent.com/35948339/139471287-756f3b15-ca54-40aa-ac66-64a846c4aa20.png) <br><br> 1️⃣ Active Profile에 test 입력
      -------------------
      - #### ![image](https://user-images.githubusercontent.com/35948339/139470684-7ea93b00-b5ee-4ca5-9553-5d4ee49f1e49.png) <br><br> 2️⃣ -Dspring.profiles.active="test" 입력
    -----------
    - #### 프로파일 표현식
      - #### ! (not)
        ``` java
          @Profile("!test")
        ```
        - #### test가 아닌 것을 프로파일을 등록한다.
      - #### & (and)
      - #### | (or)
--------
## 프로파일 사용 예
  - #### `테스트 환경에서는 A라는 Bean`을 사용하고, `배포 환경에서는 B라는 Bean`을 사용하고 싶을 때
  - #### 또는 `해당 Bean은 모니터링 용도`이므로 `테스트 할 때는 필요가 없고 배포할 때만 등록`이 되면 좋겠다.
