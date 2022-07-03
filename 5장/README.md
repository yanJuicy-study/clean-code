# 5장 형식 맞추기

- 프로그래머라면 형식을 깔끔하게 맞춰 코드를 짜야 한다.
- 코드 형식을 맞추기 위한 간단한 규칙을 정하고 착실히 따라야 한다.
- 팀으로 일한다면 합의해 규칙을 정하고 모두가 따라야 한다. 필요하다면 규칙을 자동으로 적용하는 도구를 활용한다.

⇒ 인텔리제이는 checkstyle이라는 플러그인 설치하면 가능함

### 형식을 맞추는 목적

- 코드 형식은 너무 중요하다.
    - 너무 중요해서 무시하기 어렵다.
    - 너무나도 중요하므로 융통성 없이 맹목적으로 따르면 안 된다.
- 코드 형식은 의사소통의 일환이다.
    - 의사소통은 전문 개발자의 의무다.
- 오늘 구현한 기능이 다음 버전에서 바뀔 확률은 높다.
    - 오늘 구현한 코드의 가독성은 앞으로 바뀔 코드의 품질에 많은 영향을 미친다.
    - 오랜 시간이 지나 처음의 코드가 없어져도 그 때의 구현 스타일과 가독성 수준은 유지보수 용이성과 혹장성에 계속 영향을 미친다.

⇒ 팀원들이 최대한 비슷한 형식의 코드를 작성해야 시간이 지나도 서로의 코드를 이해하기 쉬운것 같다.

### 적절항 행 길이를 유지하라

- 파일의 세로 길이는 대부분 200줄 정도로 유지해도(최대 500줄 이내) 커다란 시스템을 구축할 수 있다. (junit, ant, tomcat…)
- 반드시 지킬 엄격한 규칙은 아니지만 따라하면 좋을 것 같다.

### 신문 기사처럼 작성하라

- 소스 파일을 신문 기사와 비슷하게 작성한다.
    - 이름은 간단하면서도 설명이 가능하게 짓는다.
    - 이름만 보고도 올바른 모듈인지 아닌지를 판단할 정도로 신경을 쓴다.
    - 소스 파일 첫 부분에 고차원 개념과 알고리즘을 설명한다.
    - 아래로 내려갈수록 의도를 세세하게 묘사한다.
    

### 개념은 빈 행으로 분리하라

- 코드의 각 행은 수식이나 절을 나타내고, 일련의 행 묶음은 완결된 생각 하나를 표현한다.
- 생각 사이는 빈 행을 넣어 분리해야한다.
    - 빈 행은 새로운 개념을 시작한다는 시각적 단서다.
- 빈 행으로 분리한 코드
    
    ```java
    package fitnesse.wikitext.widgets;
    
    import java.util.regex.Matcher;
    import java.util.regex.Pattern;
    
    public class BoldWidget extends ParentWidget {
    	public static final String REGEXP = "'''.+?'''";
    	private static final Pattern pattern = Pattern.compile("'''(.+?)'''",
    		Pattern.MULTILINE + Pattern.DOTALL
    	);
    
    	public BoldWidget(ParentWidget parent, String text) throws Exception {
    		super(parent);
    		Matcher match = pattern.matcher(text);
    		match.find();
    		addChildWidgets(match.group(1));
    	}
    
    	public String render() throws Exception {
    		StringBuffer html = new StringBuffer("<b>");
    		html.append(childHtml()).append("</b>");
    
    	    return html.toString();
    	}
    }
    ```
    
- 분리가 없는 코드
    - 가독성이 어질어질하다
    
    ```java
    package fitnesse.wikitext.widgets;
    import java.util.regex.Matcher;
    import java.util.regex.Pattern;
    public class BoldWidget extends ParentWidget {
    	public static final String REGEXP = "'''.+?'''";
    	private static final Pattern pattern = Pattern.compile("'''(.+?)'''",
    		Pattern.MULTILINE + Pattern.DOTALL
    	);
    	public BoldWidget(ParentWidget parent, String text) throws Exception {
    		super(parent);
    		Matcher match = pattern.matcher(text);
    		match.find();
    		addChildWidgets(match.group(1));
    	}
    	public String render() throws Exception {
    		StringBuffer html = new StringBuffer("<b>");
    		html.append(childHtml()).append("</b>");
    	    return html.toString();
    	}
    }
    ```
    

### 세로 밀집도

- 세로 밀집도는 연관성을 의미한다.
    - 서로 밀접한 코드 행은 세로로 가까이 놓여야 한다는 뜻이다.
- 코드1
    - 필드끼리 떨어져 있다.
    
    ```java
    public class ReporterConfig {
        /**
         * 리포터 리스너의 클래스 이름
        */
        private String m_className;
    
        /**
         * 리포터 리스너의 속성
        */
        private List<Property> m_properties = new ArrayList<Property>();
        public void addProperty(Property property) {
            m_properties.add(property);
    }
    ```
    
- 코드2
    - 필드끼리 붙어 있어 보기 좋다
    
    ```java
    public class ReporterConfig {
        private String m_className;
        private List<Property> m_properties = new ArrayList<Property>();
    
        public void addProperty(Property property) {
            m_properties.add(property);
    }
    ```
    

### 수직 거리

- 서로 밀접한 개념은 세로로 가까이 둬야 한다. 물론 다른 파일에 속한다면 규칙이 통하지 않는다.
    - 함수 연관 관계와 동작 방식을 이해하려고 소스 파일을 위아래로 훑는건 나쁘다.
    - 함수나 변수가 정의된 코드를 찾으러 상속 관계를 거슬러 올라가는 것도 나쁘다.
    - 시스템의 동작 방식을 이해하려고 조각들을 찾아 헤매면 안된다.
- 타당한 근거가 없다면 밀접한 개념은 한 파일에 속해야한다.
    - 이것이 protected 변수를 피해야 하는 이유다.
    
    ⇒ protected 변수를 찾아 상속 관계를 거슬러 올라가야한다. 
    
- 같은 파일에 속할 정도로 밀접한 개념은 세로 거리로 연관성을 표현한다.
    - 연관성은 한 개념을 이해하는 데 다른 개념이 중요한 정도다.
    - 연관성이 깊은 두 개념이 멀리 떨어져 있으면 코드를 읽는 사람이 여기저기 찾아야 한다.

1. 변수 선언
- 변수는 사용하는 위치에 최대한 가까이 선언한다.

```java
private static void readPreferences() {
      InputStream is = null;
      try {
          is = new FileInputStream(getPreferencesFile());
          setPreferences(new Properties(getPreferences()));
          getPreferences().load(is);
      } catch (IOException e) {
          try {
              if (is != null)
                  is.close();
          } catch (IOException e1) {
          }
      }
  }
```

- 루프를 제어하는 변수는 루프 문 내부에 선언한다.

```java
public int countTestCases() {
      int count = 0;
      for (Test each : tests)
          count += each.countTestCases();
      return count;
  }
```

- yanju’s 코드
    
    ```java
    @Test
    	@DisplayName("스케줄 생성 성공 테스트")
    	void testCreateSchedule() {
    		RequestCreateSchedule requestCreateSchedule = new RequestCreateSchedule(1L, 1L, LocalDateTime.now(),
    			LocalDateTime.now());
    
    		Theater theater = new Theater(Region.SEOUL, "강남");
    		TheaterRoom theaterRoom = new TheaterRoom(theater, "A관");
    		given(theaterRoomRepository.findById(anyLong())).willReturn(Optional.of(theaterRoom));
    
    		Movie movie = new Movie("test", LimitAge.CHILD, Genre.ACTION, 180);
    		given(movieRepository.findById(anyLong())).willReturn(Optional.of(movie));
    
    		Schedule schedule = Schedule.builder()
    			.theaterRoom(theaterRoom)
    			.movie(movie)
    			.startTime(requestCreateSchedule.startTime())
    			.endTime(requestCreateSchedule.endTime())
    			.build();
    		given(
    			scheduleConverter.convertFromRequestCreateScheduleToSchedule(any(RequestCreateSchedule.class),
    				any(TheaterRoom.class),
    				any(Movie.class))).willReturn(
    			schedule);
    		given(scheduleRepository.save(any(Schedule.class))).willReturn(schedule);
    
    		Long id = scheduleService.createSchedule(requestCreateSchedule);
    
    		assertThat(id).isEqualTo(schedule.getId());
    	}
    ```
    

1. 인스턴스 변수
- 인스턴스 변수는 클래스 맨 처음에 선언한다.
    - 잘 설계한 클래스는 많은 메서드가 인스턴스 변수를 사용한다.
- 인스턴스 변수 위치는 논쟁이 있지만 자바는 맨 처음에 선언한다.
    - 잘 알려진 위치에 인스턴스 변수를 모은다는 사실이 더 중요하다.
- 다음과 같이 중간쯤에 인스턴스 변수를 선언하면 안된다.

```java
public class TestSuite {

	public static Constructor<? extends TestCase>
	getTestConstructor(Class<? extends TestCase> theClass)
	throws NoSuchMethod Exception {
	...
	}

	public static Test warning(final String message) {
	...
	}

	private String name;
	private Vector<Test> fTests = new Vector<Test>(10);

	public TestSuite() {
	}
}
```

1. 종속 함수
- 한 함수가 다른 함수를 호출하면 두 함수는 세로로 가까이 배치한다.
    - 가능하다면 호출하는 함수를 호출되는 함수보다 먼저 배치한다.
    - 그러면 자연스럽게 읽힌다.
    - 가독성이 좋아진다

```java
public class WikiPageResponder implements SecureResponder { 
      protected WikiPage page;
      protected PageData pageData;
      protected String pageTitle;
      protected Request request; 
      protected PageCrawler crawler;

      public Response makeResponse(FitNesseContext context, Request request) throws Exception {
          String pageName = getPageNameOrDefault(request, "FrontPage");
          loadPage(pageName, context); 
          if (page == null)
              return notFoundResponse(context, request); 
          else
              return makePageResponse(context); 
      }

      private String getPageNameOrDefault(Request request, String defaultPageName) {
          String pageName = request.getResource(); 
          if (StringUtil.isBlank(pageName))
              pageName = defaultPageName;

          return pageName; 
      }

      protected void loadPage(String resource, FitNesseContext context)
          throws Exception {
          WikiPagePath path = PathParser.parse(resource);
          crawler = context.root.getPageCrawler();
          crawler.setDeadEndStrategy(new VirtualEnabledPageCrawler()); 
          page = crawler.getPage(context.root, path);
          if (page != null)
              pageData = page.getData();
      }

      private Response notFoundResponse(FitNesseContext context, Request request)
          throws Exception {
          return new NotFoundResponder().makeResponse(context, request);
      }

      private SimpleResponse makePageResponse(FitNesseContext context)
          throws Exception {
          pageTitle = PathParser.render(crawler.getFullPath(page)); 
          String html = makeHtml(context);

          SimpleResponse response = new SimpleResponse(); 
          response.setMaxAge(0); 
          response.setContent(html);
          return response;
      } 
  ...
```

1. 개념적 유사성
- 어떤 코드는 서로 끌어당긴다.
- 친화도가 높을수록 코드는 가까이 배친한다.
    - 한 함수가 다른 함수를 호출해 생기는 직접적인 종속성은 친화도가 높다
    - 비슷한 동작을 수행하는 일군의 함수는 친화도가 높다.

```java
class Assert {
	static public void assertTrue(String message, boolean condition) {
		if (!condition) 
			fail(message);
	}
	
	static public void assertTrue(boolean condition) { 
		assertTrue(null, condition);
	}
	
	static public void assertFalse(String message, boolean condition) { 
		assertTrue(message, !condition);
	}
	
	static public void assertFalse(boolean condition) { 
		assertFalse(null, condition);
	}
```

### 세로 순서

- 일반적으로 함수 호출 종속성은 아래 방향으로 유지한다.
- 호출되는 함수를 호출하는 함수보다 나중에 배치한다.
- 신문 기사와 마찬가지로 가장 중요한 개념을 가장 먼저 표현한다.
    - 가장 중요한 개념을 표현할 때는 세세한 사항을 최대한 배제한다.
    - 세세한 사항은 가장 마지막에 표현한다.
    - 이러면 독자가 소스 파일에서 첫 함수 몇개만 읽어도 개념을 파악하기 쉬워진다.
    

### 가로 형식 맞추기

- 프로그래머는 짧은 행을 선호한다
- 120자 정도로 행 길이를 제한한다.

### 가로 공백과 밀집도

- 가로로는 공백을 사용해 밀접한 개념과 느슨한 개념을 표현한다.
- 할당문 양 옆에 공백을 넣어 두 가지 요소가 확실히 나뉜다.
- 함수와 인수는 서로 밀접하기 때문에 함수 이름과 괄호 사이에 공백을 넣지 않는다.

```java
private void measureLine(String line) { 
    lineCount++;

	// 할당 연산자가 강조되어 왼쪽/오른쪽 요소가 나뉨
    int lineSize = line.length();
    totalChars += lineSize; 
	
	// 함수와 인수는 밀접하기에 공백을 넣지 않는다.
	// 인수들은 공백으로 분리(별개라는 점을 보여줌)
    lineWidthHistogram.addLine(lineSize, lineCount);
    recordWidestLine(lineSize);
}
```

### 가로 정렬

- 아래와 같이 가로 정렬을 하면 엉뚱한 부분이 강조가 된다.
- 변수 유형은 무시하고 변수 이름부터 읽게 된다.
- 할당 연산자는 보이지 않고 오른쪽 피연산자에 눈이 간다.

⇒ SQL에서는 가로 정렬 하지 않나? (create table)

```java
public class FitNesseExpediter implements ResponseSender {
    private     Socket         socket;
    private     InputStream    input;
    private     OutputStream   output;
    private     Reques         request;      
    private     Response       response; 
    private     FitNesseContex context; 
    protected   long           requestParsingTimeLimit;
    private     long           requestProgress;
    private     long           requestParsingDeadline;
    private     boolean        hasError;

	public FitNesseExpediter(Socket          s,
							 FitNesseContext context) throws Exception
	{
		this.context =            context;
		socket =                  s;
		input =                   s.getInputStream();
		output =                  s.getOutputStream();
		requestParsingTimeLimit = 10000;
	}
```

- 가로 정렬을 하지 않으면 결함을 찾기 쉽다.

```java
public class FitNesseExpediter implements ResponseSender {
    private Socket socket;
    private InputStream input;
    private OutputStream output;
    private Request request;      
    private Response response; 
    private FitNesseContex context; 
    protected long requestParsingTimeLimit;
    private long requestProgress;
    private long requestParsingDeadline;
    private boolean hasError;

	public FitNesseExpediter(Socket s,
		FitNesseContext context) throws Exception {
		this.context = context;
		socket = s;
		input = s.getInputStream();
		output = s.getOutputStream();
		requestParsingTimeLimit = 10000;
	}
```

### 들여쓰기

- 범위로 이뤄진 계층을 표현하기 위해 코드를 들여쓴다.
- 들여쓴 정도는 계층에서 코드가 자리잡은 수준에 비례한다.
    - 파일 전체에 적용되는 정보가 있다.
    - 클래스 내 각 메서드에 적용되는 정보가 있다.
    - 블록 내 블록에 재귀적으로 적용되는 정보가 있다.
- 들여쓰기한 파일은 구조가 한눈에 들어온다.
- 간단한 if 문, 짧은 while 문, 짧은 함수에서도 들여쓰기는 반드시 한다.

```java
public class CommentWidget extends TextWidget {
    public static final String REGEXP = "^#[^\r\n]*(?:(?:\r\n)|\n|\r)?";

    public CommentWidget(ParentWidget parent, String text){super(parent, text);}
    public String render() throws Exception {return ""; } 
}
```

```java
public class CommentWidget extends TextWidget {
    public static final String REGEXP = "^#[^\r\n]*(?:(?:\r\n)|\n|\r)?";

    public CommentWidget(ParentWidget parent, String text){
        super(parent, text);
    }

    public String render() throws Exception {
        return ""; 
    } 
}
```

### 가짜 범위

- 빈 while문이나 for 문은 쓰지 않는다.
- 만약에 쓴다면 새 행에 세미콜론을 넣어준다.
    - 그렇게 해야 눈에 띈다.

```java
while (dis.read(buf, 0, readBufferSize) != -1)
;
```

### 팀 규칙

- 팀에 속한다면 팀 규칙을 따라야 한다.
