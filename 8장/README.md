# 8장 경계

## 경계란?

- 외부 코드를 내 코드에서 호출하는 부분을 경계라고한다.
- 이 외부 코드를 우리 코드에 깔끔하게 통합해야한다.

![image](https://user-images.githubusercontent.com/43159295/180601837-aa115b81-0160-40dc-b950-ab83d75dbde6.png)

## 외부 코드 사용하기

- 인터페이스 제공자는 더 많은 기능을 제공하려고 한다.
- 사용자는 자신의 요구에 집중하는 인터페이스를 원한다.
- 이런 차이로 인해 시스템 경계에서 문제가 생길 소지가 많다.

### Map에서 생길 수도 있는 문제

- map에서 제공해주는 기능은 많다.

![image](https://user-images.githubusercontent.com/43159295/180601840-ff64afe9-d74e-4076-86b8-1a59e69c1f68.png)

- 프로그램에서 Map을 만들어 넘기다고 할 때, 받는 쪽에서 Map의 clear, remove 등을 사용해서 내용을 지우는 문제가 발생할 수도 있다.
- Map은 객체 유형을 제한하지 않는다.(제네릭), 따라서 마음만 먹으면 사용자는 어떤 객체 유형도 추가할 수 있다.
    - 프로그램에서 추구하는 객체를 사용하지 않을수도 있는 문제가 생긴다.

- Map을 직접 사용한 코드의 문제점
    - 사용자가 필요하지 않은 기능까지 제공할 수 있다.
    - Map 인스턴스를 여기저기로 넘긴다면, Map 인터페이스가 변경될 때, 수정할 코드가 많아진다.

```java
Map<String, Sensor> sensors = new HashMap<Sensor>();
Sensor s = sensors.get(sensorId);

// 이런 원하지 않는 기능을 제공할 수 있다.
sensors.clear();
```

- Map을 클래스로 감싸서 사용
    - Generic이 사용되었는지 신경 안써도 됨
    - Map 인터페이스가 변경되어도 Sensors 안에서만 고치면 된다.
    - Sensors 클래스는 프로그램에서 필요한 map의 기능만 제공한다.

```java
public class Sensors {
    private Map<String, Sensor> sensors = SensorsFactory().get();
    
    public Sensor getById(String id) {
        return sensors.get(id);
    }
    
    // 이하 생략
}
```

- 추가로 알아두면 좋아 보이는 개념
    
    일급 컬렉션 
    
    [https://jojoldu.tistory.com/412](https://jojoldu.tistory.com/412)
    

## 경계 살피고 익히기

- 외부 코드를 사용하면 적은 시간에 더 많은 기능을 출시하기 쉬워진다.
- 경계 클래스를 잘 만들기 위해 외부 코드를 잘 사용할 줄 알아야 한다.
- 간단한 테스트 케이스를 작성해 외부 코드를 익혀본다. 이를 학습 테스트라고 한다.
- 학습 테스트는 프로그램에서 사용하려는 방식대로 외부 API를 호출한다.

```java
@Test // 1차 시도
public void testLogCreate() {
    Logger logger = Logger.getLogger("MyLogger");
    logger.info("hello");
}
 
@Test // 2차 시도
public void testLogAddApender() {
    Logger logger = Logger.getLogger("MyLogger");
    ConsoleAppender appender = new ConsoleAppender();
    logger.addAppender(appender);
    logger.info("hello");
}
 
@Test // 3차 시도 (완료)
public void testLogAddAppender() {
    Logger logger = Logger.getLogger("MyLogger");
    ConsoleAppender appender = new ConsoleAppender();
    logger.addAppender(new ConsoleAppender(
            new PatternLayout("%p %t %m%n"),
            ConsoleAppender.SYSTEM_OUT));
    logger.info("hello");
}
```

- 여러 시도를 통해 로그 찍는 방법을 알았으니 간단히 테스트를 해본다.

```java
public class LogTest {
    private Logger logger;
    
    @Before
    public void initialize() {
        logger = Logger.getLogger("logger");
        logger.removeAllAppenders();
        Logger.getRootLogger().removeAllAppenders();
    }
    
    @Test
    public void basicLogger() {
        BasicConfigurator.configure();
        logger.info("basicLogger");
    }
    
    @Test
    public void addAppenderWithStream() {
        logger.addAppender(new ConsoleAppender(new ParrenLayout("%p %t %m%n"), ConsoleAppender.SYSTEM_OUT));
        logger.info("addAppenderWithStream");
    }
    
    @Test
    public void addAppenderWithoutStream() {
        logger.addAppender(new ConsoleAppender(new PatternLayout("%p %t %m%n")));
        logger.intfo("addAppenderWithoutStream");
    }
}
```

## 학습 테스트는 공짜 이상이다

- 학습 테스트에 드는 비용은 없다. 오히려 API를 배워야 하므로 필요한 지식만 확보하는 손쉬운 방법이다.
- 외부 패키지의 새 버전이 나온다면 학습 테스트를 돌려 차이가 있는지 확인한다.
- 새 버전이 우리 코드와 호환되지 않으면 학습 테스트가 이 사실을 곧바로 밝혀낸다.
- 학습 테스트를 사용하지 않아도, 실제 코드룰 이용해 패키지를 사용하는 테스트 케이스가 필요하다.

## 아직 존재하지 않는 코드를 사용하기

- 경계와 관련해서 아는 코드와 모르는 코드를 분리하는 경계도 필요하다.
- 우리의 지식이 경계를 너머 미치지 못하는 코드 영역도 있다.
    - 외부 무선 통신(송신기)과 관련한 코드는 지식이 없어서 봐도 모른다.
- 이때는 자체적으로 인터페이스를 정의하고 그것을 사용한다.
    - 인터페이스를 전적으로 통제할 수 있다는 장점
    - 코드 가독성과 코드 의도가 분명해진다는 장점
    - 제공측에서 API를 전달해주면 Adapter 패턴을 이용

![image](https://user-images.githubusercontent.com/43159295/180601851-98b5bcbf-cd19-4ec8-a822-8b1da82f26ae.png)

## 깨끗한 경계

- 경계에서는 변경이 많이 벌어진다.
- 우리의 설계가 우수하다면 변경에 의한 재작업이 필요하지 않다.
- 통제하지 못하는 코드를 사용할 때는 변경 비용이 커지지 않도록 주의해야 한다.
- 경계에 위치하는 코드는 깔끔히 분리한다.
- 기대치를 정의하는 테스트 케이스도 작성한다.
- Map에서 봤듯이 새로운 클래스로 경계를 감싸거나 Adapter 패턴을 사용해 우리가 원하는 인터페이스를 패키지가 제공하는 인터페이스로 변환하자.
