## @Autowired
  - #### 필요한 의존 객체의 `타입`에 해당하는 Bean을 주입한다.
  - #### `required` : 기본 값은 `true` ➡ 그러므로, Bean을 주입 할 수 없다면 에러 발생‼
  ``` java
    @Service
    public class BookService {
        BookRepository bookRepository;

        @Autowired(required = false)
        public void setBookRepository(BookRepository bookRepository) {
            this.bookRepository = bookRepository;
        }
    }
    
    /////////////////////
    
    public class BookRepository {
      ....
    }
  ```
  - #### @BookRepository `required = false` 라면, BookRepository 의존성 주입 없이 <br><br> BookService의 `Bean이 생성된다.`
  ----------
  - #### 만약, 같은 타입의 Bean이 여러 개 일때는 BookService 에는 어떤 것을 먼저 @Autowired 할까?
  ``` java
    public interface BookRepository {
      ...
    }
    
    //////////
    
    @Repository
    public MyBookRepository implements BookRepository {
      ...
    }
    
    @Repository
    public YourBookRepository implements BookRepository {
      ...
    }
  ```
  - #### Spring은 의존성 주입을 어떤걸 해줘야 하는지 모르기 때문에 에러 발생 ❌
  ``` java
    @Repository
    @Primary
    public MyBookRepository implements BookRepository {
      ...
    }
    
    @Repository
    public YourBookRepository implements BookRepository {
      ...
    }
  ```
  - #### ⭐1️⃣ @Primary를 사용하여 정해줄 수 있다.
  --------------
  - #### 어떤 @Repository가 주입되었는지 확인하기 위해 `ApplicacationRunner`를 사용해 보자
  ``` java
    @Service
    public class BookService {
    
        @Autowired
        BookRepository bookRepository;
        
        // bookRepository 의존성을 주입받은 class 이름 출력하기
        public void printBookRepository() {
            System.out.println(bookRepository.getClass());
        }
    }
    
    /////////////////////////////////
    
    // ApplicacationRunner
    public class BookServiceRunner implements ApplicacationRunner {
    
        @Autowired
        BookService bookService;
        
        @Override
        public void run(ApplicationArguments args) throws Exception {
            bookService.printBookRepository();
        }
    }
  ```
  - #### @Primary를 붙힌 클래스의 이름이 출력된다.
  ```
    class com.example.demospring51.MyBookRepository
  ```
  ----------
  - #### 2️⃣ @Qualifier("myBookRepository")에 이름을 주어서 정해줄 수 있다. (클래스 이름은 소문자로 전해줘야한다)
  ``` java
    @Service
    public class BookService {

        @Autowired @Qualifier("myBookRepository")
        BookRepository bookRepository;
    }
    ...
  ```
  ```
    class com.example.demospring51.MyBookRepository
  ```
  - #### 1️⃣ @Primary가 2️⃣ @Qualifier 보다 더 `Type Safe`하다. <br><br> `Type Safe`의 의미는 `변수에 잘못된 값을 전달하면 에러가 발생` 하는 것을 의미하는데, <br><br> @Qualifier에 클래스 이름을 전달할 때, `오타 혹은 없는 이름을 전달하게 되면`, 구동하기 전까지 에러를 알아차리기 힘들다.
  -----------
  - #### 3️⃣ 해당 타입의 Bean을 전부 주입 받기
  ``` java
    @Autowired
    List<BookRepository> bookRepositoryList;
    
    public void printBookRepository() {
        this.bookRepositoryList.forEach(System.out::println);
    }
  ```
  ```
    class com.example.demospring51.MyBookRepository
    class com.example.demospring51.YourBookRepository
  ```
  - #### List를 사용해서 @Repository를 갖고 있는 모든 클래스를 출력할 수 있다.
  ----------
  - ### @Primary, @Qualifier 등이 어떻게 동작하는 것 일까?
    - #### `BeanPostProccessor`에 의해 동작된다.
    - #### `Bean의 인스턴스를 만든 다음`에 `Bean의 initialization 라이프 사이클 이후`, `부가적인 작업을 할 수 있는 라이프사이클`이 있는데 <br><br> 그것이 `BeanPostProccessor` interface이다.
    - `BeanPostProccessor`은 `새로 만든 Bean을 수정할 수 있는 라이프 사이클이다.`
