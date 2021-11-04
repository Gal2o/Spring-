## 국제화 (i18n) 기능을 제공하는 인터페이스
``` java
  ApplicationContext extends MessageSource {
    ...
  }
```
- #### getMessage(String code, Object[] args, String, default, Locale, loc)
- #### ApplicationContext는 Bean 주입 시, 본인도 주입하므로 MessageSource도 그냥 @Autowired가 가능하다
--------
## Spring Boot에서 국제화 (i18n)
  - #### messages.properties
  ``` java
    greeting=Hello, {0}
  ```
  -------
  - #### messages_en_US.properties `(하이픈을 꼭 써야 인식을 한다)`
  ``` java
    greeting=Hello, {0}
  ```
  --------
  - #### messages_ko_KR.properties
  ``` java
    greeting=안녕, {0}
  ```
  -----------
  - #### 빌드하여 실행 시켜보기
  ``` java
    @Component
    public class AppRunner implements ApplicationRunner {

        @Autowired
        MessageSource messageSource;

        @Override
        public void run(ApplicationArguments args) throws Exception {
            Locale.setDefault(Locale.ENGLISH);
            System.out.println(messageSource.getMessage("greeting", new String[]{"min"}, Locale.getDefault()));
            System.out.println(messageSource.getMessage("greeting", new String[]{"min"}, Locale.ENGLISH));
            System.out.println(messageSource.getMessage("greeting", new String[]{"min"}, Locale.KOREA));
        }
    }
  ```
  - #### default Locale이 한국이므로, Default가 한국어다. <br><br> `Locale.setDefault(Locale.ENGLISH)`를 설정해서 기본 Locale을 영어로 바꿀 수 있다.
  ```
    Hello, min
    Hello, min
    안녕, min
  ```
  -------------
  - ### Spring Boot에서 Reload 기능 사용해보기
  ``` java
    @SpringBootApplication
    public class Demospring51Application {
        public static void main(String[] args) {
            SpringApplication.run(Demospring51Application.class, args);
        }


        @Bean
        public MessageSource messageSource() {
            var messageSource = new ReloadableResourceBundleMessageSource();
            messageSource.setBasename("classpath:/messages");
            messageSource.setDefaultEncoding("UTF-8");
            messageSource.setCacheSeconds(3);
            return messageSource;
        }
    }
  ```
  - #### `ReloadableResourceBundleMessageSource` 클래스를 이용하여 Reload가 가능한 MessageSouce 인스턴스를 만든다.
  - #### Build 되어 있는 `target 폴더 안에 properties를 읽어 오는 것`이기 때문에, `내용을 수정하려면 Build를 해야 적용`이 된다.
  ```
    Hello, min
    Hello, min
    안녕, min
    Hello, min
    Hello, min
    반갑습니다, min
  ```
