## ApplicationEventPublisher
  - #### 이벤트 프로그래밍에 필요한 인터페이스 제공
  - #### `옵저버 패턴` 구현체
  ``` java
  public interface ApplicationContext extends ApplicationEventPublisher {
    ...
  }
  ```
  - #### `ApplicationEventPublisher` 또한 `ApplicationContext`를 상속 받는다.
--------
  - ### Spring 4.2 이전 버전에서 Event 구현하기
    - #### MyEvent.java
    ``` java
      public class MyEvent extends ApplicationEvent {

          private int data;

          public MyEvent(Object source) {
              super(source);
          }

          public MyEvent(Object source, int data) {
              super(source);
              this.data = data;
          }

          public int getData() {
              return data;
          }
      }
    ```
    --------
    - #### AppRunner.java
    ``` java
      @Component
      public class AppRunner implements ApplicationRunner {
      
          @Autowired
          ApplicationEventPublisher publisher;

          @Override
          public void run(ApplicationArguments args) throws Exception {
              publisher.publishEvent(new MyEvent(this, 100));
          }
      }
    ```
    - #### publishEvent로 이벤트를 발생 시킨다.
    --------
    - #### MyEventHandler.java
    ``` java
      @Component
      public class MyEventHandler implements ApplicationListener<MyEvent> {

          @Override
          public void onApplicationEvent(MyEvent event) {
              System.out.println("이벤트 받음 ! 데이터는 " + event.getData());
          }
      }
    ```
    - #### 등록되어 있는 @Bean 중에서 이벤트를 받는다.
    ```
      이벤트 받음 ! 데이터는 100
    ```
  ---------
  - ### Spring 4.2 이상에서 Event 구현하기
    - #### 더 이상 `ApplicationEvent`를 상속 받지 않아도 이벤트를 구현할 수 있다.
      - #### MyEvent.java
      ``` java
        public class MyEvent {

            private int data;

            private Object source;

            public MyEvent(Object source, int data) {
                this.source = source;
                this.data = data;
            }

            public Object getSource() {
                return source;
            }

            public int getData() {
                return data;
            }
        }
      ```
      - #### AppRunner.java
      ``` java
        @Component
        public class AppRunner implements ApplicationRunner {

            @Autowired
            ApplicationEventPublisher publisher;

            @Override
            public void run(ApplicationArguments args) throws Exception {
                publisher.publishEvent(new MyEvent(this, 100));
            }
        }
      ```
      - #### MyEventHandler.java
      ``` java
        @Component
        public class MyEventHandler {

            @EventListener
            public void handle(MyEvent event) {
                System.out.println(Thread.currentThread().toString());
                System.out.println("이벤트 받음 ! 데이터는 " + event.getData());
            }
        }
      ```
      ```
        이벤트 받음 ! 데이터는 100
      ```
    ------------
    - ### 여러 이벤트 핸들러 중에서 우선순위 만들어주기
      - #### MultiThread 이므로, 어떤 Handler가 먼저 출력 될지 모른다.
      - #### 1️⃣ @Order 사용하기
      ``` java
        @Component
        public class MyEventHandler {

            @EventListener
            @Order(Ordered.HIGHEST_PRECEDENCE)
            public void handle(MyEvent event) {
                System.out.println(Thread.currentThread().toString());
                System.out.println("이벤트 받음 ! 데이터는 " + event.getData());
            }
        }
        
        /////////////////
        
        @Component
        public class AnotherHandler {

            @EventListener
            @Order(Ordered.HIGHEST_PRECEDENCE + 2)
            public void handle(MyEvent myEvent) {
                System.out.println(Thread.currentThread().toString());
                System.out.println("Another " + myEvent.getData());
            }
        }
      ```
      - #### AnotherHandler에 +2 했으므로, AnotherHandler가 마지막 순서가 된다.
    ----------------
    - ### 2️⃣ @Async 사용하여 비동기로 실행하기
      ``` java
        @Component
        public class MyEventHandler {

            @EventListener
            @Async
            public void handle(MyEvent event) {
                System.out.println(Thread.currentThread().toString());
                System.out.println("이벤트 받음 ! 데이터는 " + event.getData());
            }
        }

        /////////////////

        @Component
        public class AnotherHandler {

            @EventListener
            @Async
            public void handle(MyEvent myEvent) {
                System.out.println(Thread.currentThread().toString());
                System.out.println("Another " + myEvent.getData());
            }
        }
      ```
      - #### ⭐ @Async를 사용하려면 @EnableAsync를 사용 해야한다.
        ``` java
          @SpringBootApplication
          @EnableAsync
          public class Demospring51Application {
              public static void main(String[] args) {
                  SpringApplication.run(Demospring51Application.class, args);
              }
          }
        ```
        ```
          Thread[task-1,5,main]
          Thread[task-2,5,main]
          Another 100
          이벤트 받음 ! 데이터는 100
        ```
    ------------
    - ### Spring이 제공하는 기본 이벤트 사용해보기
      ``` java
        @Component
        public class MyEventHandler {

            @EventListener
            @Async
            public void handle(MyEvent event) {
                System.out.println(Thread.currentThread().toString());
                System.out.println("이벤트 받음 ! 데이터는 " + event.getData());
            }

            @EventListener
            @Async
            public void handle(ContextRefreshedEvent event) {
                System.out.println(Thread.currentThread().toString());
                System.out.println("ContextRefreshedEvent");
            }

            @EventListener
            @Async
            public void handle(ContextClosedEvent event) {
                System.out.println(Thread.currentThread().toString());
                System.out.println("ContextClosedEvent");
            }
        }
      ```
      - #### ContextRefreshedEvent : ApplicationContext를 `초기화 or Refresh` 했을 때 발생
      - #### ContextStartedEvent : ApplicationContext를 start() 하여 `라이프사이클 Bean들이 시작 신호`를 받은 시점에 발생
      - #### ContextStoppedEvent : ApplicationContext를 stop() 하여 `라이프사이클 Bean들이 정지 신호`를 받은 시점에 발생
      - #### ContextClosedEvent : ApplicationContext를 close() 하여 싱글톤 `Bean 소멸 되는 시점`에 발생
      - #### RequestHandledEvent : HTTP 요청을 처리 했을 때 발생
      ```
        Thread[task-1,5,main]
        ContextRefreshedEvent
        Thread[task-3,5,main]
        이벤트 받음 ! 데이터는 100
        Thread[task-2,5,main]
        Another 100
        // 서비스를 종료하면 출력된다.
        Thread[task-4,5,main]
        ContextClosedEvent
      ```
