- ## Scope
  - #### Singleton
  - #### Prototype
    - #### `매번 새로운 객체를 만들어서 사용`
    - #### Request
    - #### Session
    - #### Websocket
    - ....
  ----------------
  - ### 프로토타입 Bean이 싱글톤 Bean을 참조하면?
  ``` java
    @Component
    @Scope("prototype")
    public class Proto {

        @Autowired
        Single single;
    }
  ```
  - #### ✔ 아무 문제 없음 (하나 뿐인 동일한 인스턴스를 가져올 것이다)
  ------------
  - ### 싱글톤 Bean이 프로토 타입 Bean을 참조하면?
    - #### 프로토타입 Bean이 갱신이 안된다 ❌
    ``` java
      @Component
      public class Single {
      
          // 처음 받은 인스턴스 그대로
          @Autowired
          private Proto proto;

          public Proto getProto() {
              return proto;
          }
      }
    ```
    - #### `Single` 이 인스턴스를 할당 받을 때, `Proto`도 이미 할당 받은 상태, <br><br> `새로운 인스턴스가 Proto에 할당 될 때`, `여전히 Single은 처음 할당 받은 인스턴스`를 가르키고 있음
    - ### 예시
    ``` java
      @Component
      public class AppRunner implements ApplicationRunner {

          /* ⭐ApplicationContext 는 Bean을 만들면서 자기 자신도 주입하므로,
               @Component 등록 없이 @Autowired 가능하다.
          */
          @Autowired
          ApplicationContext ctx;

          @Override
          public void run(ApplicationArguments args) throws Exception {
              System.out.println("Proto");
              System.out.println(ctx.getBean(Proto.class));
              System.out.println(ctx.getBean(Proto.class));
              System.out.println(ctx.getBean(Proto.class));

              System.out.println("Single");
              System.out.println(ctx.getBean(Single.class));
              System.out.println(ctx.getBean(Single.class));
              System.out.println(ctx.getBean(Single.class));

              System.out.println("Proto -> Single");
              System.out.println(ctx.getBean(Single.class).getProto());
              System.out.println(ctx.getBean(Single.class).getProto());
              System.out.println(ctx.getBean(Single.class).getProto());
          }
      }
    ```
    ```
      Proto
      com.example.demospring51.Proto@39ab59f8
      com.example.demospring51.Proto@64e92d61
      com.example.demospring51.Proto@111610e6
      
      Single
      com.example.demospring51.Single@4ad4936c
      com.example.demospring51.Single@4ad4936c
      com.example.demospring51.Single@4ad4936c
      
      Proto -> Single
      com.example.demospring51.Proto@29d37757
      com.example.demospring51.Proto@29d37757
      com.example.demospring51.Proto@29d37757
    ```
    - #### 🔼Proto의 인스턴스 값은 계속 바뀌지만, Single을 통해 받은 Proto 인스턴스는 업데이트가 되지 않아 계속 동일하다.
    -----------
    - #### Proto의 인스턴스를 업데이트 시켜주려면?
      - #### 1️⃣ Scoped-Proxy
        ``` java
          @Component
          @Scope(value = "prototype", proxyMode = ScopedProxyMode.TARGET_CLASS)
          public class Proto {
          }
        ```
        - #### CGlib을 이용한 프록시를 사용하여 인스턴스를 업데이트 할 수 있다. <br><br> <img src="https://user-images.githubusercontent.com/35948339/139299260-90ca8ec8-7d39-48a0-bcfd-2c3e690cfeb7.png" width=400>
          - #### 1️⃣`Proxy 인스턴스는 Proto를 상속받기 때문에 같은 인스턴스 값`을 가진다.
          - #### 2️⃣ `Proxy 인스턴스를 Bean으로 만든다.`
          - #### 3️⃣ `Single에서는 이 Proxy 인스턴스를 주입 받아 업데이트`가 가능해진다.
      ------------
      - #### 2️⃣ Object-Provider, 3️⃣ `Provider (표준)`
        ``` java
          @Component
          public class Single {

              @Autowired
              private ObjectProvider<Proto> proto;

              public Proto getProto() {
                  return proto.getIfAvailable();
              }
          }
        ```
        ```
          Proto
          com.example.demospring51.Proto@32fdec40
          com.example.demospring51.Proto@6813a331
          com.example.demospring51.Proto@1bd81830
          
          Single
          com.example.demospring51.Single@39ab59f8
          com.example.demospring51.Single@39ab59f8
          com.example.demospring51.Single@39ab59f8
          
          Proto -> Single
          com.example.demospring51.Proto@29d37757
          com.example.demospring51.Proto@25cc7470
          com.example.demospring51.Proto@79b663b3
        ```
        - #### Single에서 ObjectProvider 또는 Provider를 사용하여 업데이트를 할 수 있다.
        ----------
-----------
- ## 싱글톤 객체 사용시 주의할 점
  - #### 실제로 싱글톤 이외의 Scope를 쓸 경우가 거의 없다. 하지만 저런 상황이 발생 한다면, <br><br>  `생명 주기가 긴 Scope (싱글톤)` ➡ `짧은 주기의 Scope (프로토타입) Bean`을 주입 받을 때 <br><br> `업데이트에 유의하자 ‼`
  - #### `프로퍼티가 공유` ➡ `Thread Safe` 하게 구현해야 한다 ‼ (데이터 동기화)
  - #### 모든 싱클톤 Scope의 Bean들은 `ApplicationContext를 만들 때`, 만들게 되어있다.
