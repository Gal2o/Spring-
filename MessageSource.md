## 국제화 (i18n) 기능을 제공하는 인터페이스
``` java
  ApplicationContext extends MessageSource {
    ...
  }
```
- #### getMessage(String code, Object[] args, String, default, Locale, loc)
- #### ApplicationContext는 Bean 주입 시, 본인도 주입하므로 MessageSource도 그냥 @Autowired가 가능하다
