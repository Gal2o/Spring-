## Spring IoC 컨테이너
  - ### Inversion of Control : `의존 관계 주입 (Dependency Injection)`이라고도 하며, <br><br> 어떤 객체가 사용하는 `의존 객체를 직접 만들어 사용하는 것이 아니라`, `주입 받아 사용하는 방법`을 말한다
  - #### == `BeanFactory`
  - #### 애플리케이션 컴포넌트의 중앙 저장소
  - #### `Bean 설정 소스`로 부터 `Bean 설정을 읽어들이고`, Bean을 구성 및 제공한다.
---------
## Bean
  - #### `Spring IoC 컨테이너`가 관리하는 객체
  - ### 장점
    - #### 의존성 주입을 하고 싶다면 `Bean이 되어야 한다.`
    - #### Scope
      - #### 애플리케이션 전반적으로 인스턴스는 오직 하나만 사용해야 한다 <br><br> 굳이 여러 개일 필요가 없다 ➡ `Singleton scope`
        - #### 예시 1️⃣. BookRepository 객체를 새로 계속 만든다면, <br><br> `DB에 최초 연결되어 Connection 객체를 생성하는 비용이 크다.` <br><br> 그러므로 `이러한 객체를 pool로 관리`를 하고, `미리 만들어 둔 객체를 재사용`하여 비용을 줄인다.
        - #### 예시 2️⃣. 만약 어떠한 서비스에 대해 `@Mock을 사용하여 Test를 하고 싶은데`, <br><br> 만약, `객체에 의존성을 직접 주입을 하는 과정을 반복한다면` , 번거로울 뿐만 아니라 비용면에서도 비효율적이다. <br><br> Singleton 패턴을 이용하여 객체를 `재사용하여 언제든지 Test가 가능하다.`
      - #### Prototype : Singleton scope와 달리 `매번 다른 객체`
      - #### 라이프사이클 인터페이스 <br><br> ex. `Bean이 만들어지는 과정에서` 부가적인 기능을 추가할 수 있다
      ``` java
        @PostConstruct
        public void postConstruct() {
          ....
        }
      ```
