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
    - #### `클래스에 정의하기`
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

--------
## 프로파일 사용 예
  - #### `테스트 환경에서는 A라는 Bean`을 사용하고, `배포 환경에서는 B라는 Bean`을 사용하고 싶을 때
  - #### 또는 `해당 Bean은 모니터링 용도`이므로 `테스트 할 때는 필요가 없고 배포할 때만 등록`이 되면 좋겠다.
