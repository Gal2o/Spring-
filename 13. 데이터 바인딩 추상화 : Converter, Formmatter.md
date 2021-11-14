## Converter
  - #### S 타입을 T 타입으로 변환할 수 있는 매우 일반적인 변환
  - #### `상태 정보 없음` == Stateless == Thread-Safe
  - #### `ConverterRegistry`에 등록하여 사용한다.
  ``` java
    public class StringToEventConverter implements Converter<String, Event> {
    
       @Override
       public Event convert(String source) {
           Event event = new Event();
           event.setId(Integer.parseInt(source));
           return event;
       }
    }
  ```
-------
## Formatter
  - #### PropertyEditor 대체제
  - #### Object ↔ String 간 변환
  - #### 문자열을 Locale에 따라 localization하는 기능도 제공한다. (Optional)
  - #### `FormatterRegistry`에 등록하여 사용
  ``` java
    public class EventFormatter implements Formatter<Event> {
       
       @Override
       public Event parse(String text, Locale locale) throws ParseException {
           Event event = new Event();
           int id = Integer.parseInt(text);
           event.setId(id);
           return event;
       }
       
       @Override
       public String print(Event object, Locale locale) {
           return object.getId().toString();
       }
    }
  ```
--------
