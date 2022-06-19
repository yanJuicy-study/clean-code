# 3장 함수

### 작게 만들어라

함수를 만드는 가장 중요한 규칙은 작게 만드는 것이다.

목록 3-2

```java
public static String renderPageWithSetupsAndTreadowns(PageDate pageDate, boolean isSuite) throws Exception{
	boolean isTestPage = pageData.hasAttribute("Test");
	if(isTestPage){
		WikiPage testPage = pageData.getWikiPage();
		StringBuffer newPageContent = new StringBuffer();
		includeSetupPages(testPage, newPageContent, isSuite);
		newPageContent.append(pageData.getContent());
		includeTeardownPages(testPage, newPageContent, isSuite);
		pageData.setContent(newPageContent.toString());
	}
}
```

목록3-3은 3-2를 줄인것이다. 함수는 목록 3-3처럼 만들어야한다.

목록 3-3

```java
public static renderPageWithSetupsAndTreadowns(PageData pageData, boolean isSuite) throws Exception{
    if(isTestPage(pageData))
        includeSetupAndTreadownPages(pageData, isSuite);
    return pageData.getHtml();
}
```

if 문 / else 문 / while 문 에 들어가는 블록은 한줄이어야 한다.

---

### 한가지만 해라

**함수는 한 가지를 해야한다. 그 한 가지를 잘 해야 한다. 그 한 가지만을 해야한다.**

함수가 ‘한 가지’만 하는지 판단하는 방법

1. 지정된 함수 이름 아래에서 추상화 수준이 하나인 단계만 수행하면 그 함수는 한 가지 작업만 한다.
2. 의미 있는 이름으로 다른 함수를 추출할 수 있다면 그 함수는 여러 작업을 하는 셈이다.
3. 한가지 작업만 하는 함수는 자연스럽게 섹션으로 나누기 어렵다.

---

### 함수 당 추상화 수준은 하나로!

함수가 ‘한 가지’ 작업만 하려면 함수 내 모든 문장의 추상화 수준이 동일해야 한다.

추상화 수준이란?

해당 코드를 읽으면서 파악할 수 있는 정보의 수준을 말한다.

- 높은 추상화 수준 :`getHtml()` 이라는 함수는 Html 을 가져오는 것은 알겠는데, 어떤 것이랑 연관되어 있는지 알 수 없기 때문에 추상화 수준이 높음.
- 보통 추상화 수준 : `String pagePathName = PathParser.render(pagepath);`
는 PathParser객체의 render 함수를 이용해 pagePathName을 가져올 수 있다는 정보를 유추할 수 있기 때문에 추상화 수준이 보통.
- 낮은 추상화 수준 : `.append("\n")` 는 바로 어떤 의미인지 유추 가능하기 때문에 추상화 수준이 낮음.

**내려가기 규칙**

코드는 위에서 아래로 이야기처럼 읽혀야 좋다. 한 함수 다음에는 추상화 수준이 한 단계 더 낮은 함수가 와야한다. 위에서 아래로 코드를 읽으면 함수 추상화 수준이 한 번에 한 단계씩 낮아집니다.

---

### Switch 문

본질적으로 switch문은 N가지를 처리하기 때문에 ‘한 가지’ 작업만 하기에는 쉽지 않습니다.

각 switch 문을 저차원 클래스 숨기고 절대로 반복하지 않는 방법은 있다. 

개선 전 코드

```java
public Money calculatePay(Employee e) throws InvalidEmployeeType {
  switch (e.type) {
    case COMMISIONED :
      return calculateCommissionedPay(e);
    case HOURLY :
      return calculateHourlyPay(e);
    case SALARIED :
      return calculateSalariedPay(e);
    default :
      throw new InvalidEmployeeType(e.type);
  }
}
```

위 함수에는 몇가지 문재가 있다.

1. 함수가 길다. 새 진원 유형을 추가하면 더 길어진다.
2. type에 따라 다른 함수들을 호출하기 때문에 한 가지 작업만 수행하지 않는다.(SRP(Single Responsibility Principle : 단일 책임 원칙)를 위반한다.)
3. OCP(Open Closed Principle : 개방 폐쇄 원칙)를 위반한다.
- 기존의 코드를 변경하지 않으면서 기능을 추가할 수 있도록 설계되어야 한다.
4. 위 함수와 구조가 동일한 함수가 무한정 존재한다.

개선 후 코드

```java
public abstract class Employee {
  public abstract boolean isPayday();
  public abstract boolean calculatePay();
  public abstract boolean deliverPay(Money pay);
}

public interface EmployeeFactory {
  public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType;
}

public class EmployeeFactoryImpl implements EmployeeFactory {
  public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType {
    switch (r.type) {
      case COMMISIONED :
        return new CommissionedEmployee(r);
      case HOURLY :
        return new HourlyEmployee(r);
      case SALARIED :
        return new SalariedEmployee(r);
      default :
        throw new InvalidEmployeeType(r.type);
    }
  }
}
```

다양한 직원 유형의 객체를 생성해줌으로써, 개선 전의 문제들을 해결할 수 있다.

---

### 서술적인 이름을 사용하라!

함수가 작고 단순할수록 서술적인 이름을 고르기도 쉬워진다.

서술적인 이름을 사용하면 개발자 머릿속에서도 설계가 뚜렷해지므로 코드를 개선하기 쉬워진다.

또한, 이름을 붙일 때는 일고나성이 있어야 한다. 모듈 내에서 함수 이름은 같은 문구, 명사, 동사를 사용한다.

---

### 함수 인수

함수에서 이상적인 인수의 개수는 0개 이다.

인수의 개수가 늘어날 수록 함수를 이해하기 어렵기 때문에 4개 이상은 특별한 이유가 있어도 사용하지 않는 것이 좋다.

최선은 입력 인수가 없는 경우이며, 차선은 입력 인수가 1개뿐인 경우이다.

**많이 쓰는 단항 형식**

함수에 인수 1개를 넘기는 이유는 다음 2가지 이다.

- 인수에 질문을 던지는 경우
ex) boolean fileExists(”MyFile”);
- 인수를 변환해 결과를 반환하는 경우
ex) InputStream fileOpen(“MyFile”)

드물게 사용되지만 이벤트 함수도 단항 함수 형식으로 이루어져있다. 이벤트 함수는 이벤트라는 사실이 코드에 명확이 들어나야한다.

**플래그 인수**

플래그 인수는 함수가 한번에 여러가지를 처리하는 것을 유추할 수 있기 떄문에 사용하지 않는 것이 좋다.

**이항 함수**

인수가 2개인 함수는 인수가 1개인 함수보다 이해하기 어렵고 헷갈리기 쉽다. Point p = new Point(0,0) 처럼 인수 2개가 한 값을 당연하게 여겨지게 사용하는 경우가 아니라면 사용을 지양해야한다.

**삼항함수**

삼항 함수는 인수가 2개인 경우보다어렵기 떄문에 만들때 신중히 고려해서 만들어야 한다.

**인수 객체**

인수가 2-3개 필요하다면 일부를 독자적인 클래스 변수로 선언할 가능성을 짚어본다.

Circle makeCircle(double x, double y, double radius) -> Circle makeCircle(Point center, double radius)

위와 같이 이항 함수 부분에서 x,y 좌표를 Point로 클래스를 생성해 볼 수 있다.

**인수 목록**

`string.format`처럼 인수 개수가 가변적인 함수도 필요하다.

`public String format(String format, Object... args)`

위의 String.format 구조를 보면 이항함수 라는 것을 알 수 있다. 하지만 가변 인수를 포함해 인수 개수가 3개가 넘어가면 안된다.

**동사와 키워드**

단항 함수는 함수와 인수가 동사/명사 쌍을 이뤄야 한다.

ex) `write(name)` / `writeFiled(name)`

또는 함수 이름에 키워드를 추가하는 방법도 있다

ex) `assertXepectedEqualsActual(expected, actual)`

---

### 부수 효과를 일으키지 마라!

부수 효과(Side Effect) : 함수 내의 실행으로 함수 회부가 영향 받는 형상

함수 이름으로 명시된 ‘한 가지’ 일 외에 다른 일이 포함되면 부수 효과가 발생하는 경우를 조심해야 한다.

한가지 일을 하게 만드는 것이 좋지만 여의치 않다면 차라리 함수 이름에 다른 일에 대한 것을 분명히 명시하는 것이 좋다.

**출력 인수**

일반적으로 인수를 함수의 입력, 반환값을 출력으로 생각하기 때문에 인수를 출력으로 사용하지 않게 주의해야 한다.

---

### 명령과 조회를 분리하라!

함수는 뭔가를 수행하거나 뭔가에 답하거나 둘중 하나만 해야한다.

예를 들어, 속성을 찾아 값을 value로 설정한 후 성공하면 true, 실패하면 false를 반환하는 함수 `public boolean set(String attribute, String value)`가 있다고 했을 때,

`if (set("username", "unclebob")) ...` 같은 경우는 username 속성이 unclebob으로 설정되어 있다면... 으로 오해할 가능성이 있습니다.  

그래서 set이 설정과 체크 두 개의 일을 하는 것 보다 `attributeExists, setAttribute`로 일을 나누는 것이 낫습니다.

---

### 오류 코드보다 예외를 사용하라!

함수에서 오류 코드를 사용하게 되면 **오류 코드에 관련된 처리를 추가적으로 해주어야 합니다.**

오류 코드 대신 예외를 사용하면 오류 처리 코드가 분리되므로 코드가 더 깔끔해집니다.

**Try/Catch 블록 뽑아내기**

try/catch 블록은 정상 동작과 오류 처리 동작이 섞이게 됩니다. 그래서 try/catch 블록을 아래와 같이 별도 함수로 분리하는 편이 좋습니다.

```java
public void delete(Page page) {
    try {
        deletePageAndAllReferences(page);
    } catch (Exception e) {
        logError(e);
    }
}

private void deletePageAndAllReferences(Page page) throws Exception {
    deletePage(page);
    registry.deleteReference(page.name);
    configKeys.deleteKey(page.name.makeKey());
}

private void logError(Exception e) {
    logger.log(e.getMessage());
}
```

**오류 처리도 한 가지 작업이다.**

함수는 ‘한 가지’ 일만 해야 하는데, 오류 처리도 한 가지 작업에 속합니다. 떄문에 위의 예시와 같이 오류를 처리하는 함수는 오류만 처리해야 한다.

---

### 반복하지 마라!

많은 원칙과 기법이 중복을 없애거나 제어할 목적으로 나왔습니다. 중복이 발생하면 코드의 길이 뿐만 아니라 오류가 발생할 확률도 높아집니다.

***AOP(Aspect Oriented Programming)** : 관점 지향 프로그래밍. 어떤 로직을 기준으로 핵심적인 관점, 부가적인 관점으로 나누어서 보고 그 관점을 기준으로 각각 묘듈화 하는 것을 말합니다.*

***COP(Component Oriented Programming)** : 컴포넌트 지향 프로그래밍입니다. 프론트 엔드에서 반복되는 요소들을 컴포넌트로 분리하여 애플리케이션을 더 빠르게 구축할 수 있게 해주는 것을 말합니다.*

---

### 구조적 프로그래밍

모든 함수와 함수 내 모든 블록에는 입구와 출구가 하나만 존재해야한다.

`break, contunue`는 사용해선 안되고 `goto`는 절대 사용하면 안된다

하지만 함수를 작게 만든다면 때로는 `return, break, continue`를 사용하는게 의도를 표현하기 쉬워지기 때문에 적절히 사용하면 괜찮다.

---

### 함수를 어떻게 짜죠?

프로그램을 만들 때 처음부터 함수를 분리하는것은 어렵다. 일단 만들어놓고 천천히 코드를 다듬고 함수로 분리하는 작업을 거쳐야 한다.
