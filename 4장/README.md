## 들어가면서

---

> 나쁜 코드에 주석을 달지 마라. 새로 짜라. - 브라이언 W. 커니핸.P.J.플라우거
> 

### 저자의 주장

- 주석은 ‘순수하게 선하지’ 못하다.
- 코드로 의도를 표현하지 못해, **실패**를 만회하기 위해 주석을 사용한다.
    - 주석은 언제나 실패를 의미한다.
- 주석은 **거짓말**을 한다.
    - 프로그래머들이 주석을 유지하고 보수하기란 현실적으로 불가능하니까.
    - 코드는 변화하고 진화한다.

<aside>
💡 **진실**은 **코드**에만 존재한다.

따라서 **코드를 깔끔하게 정리하고 표현력을 강화**하는 방향 혹은 **애초에 주석이 필요 없는 방향**으로 노력해야한다.

</aside>

## 주석은 나쁜 코드를 보완하지 못한다.

---

### 코드에 주석을 추가하는 일반적인 이유

코드 품질이 나쁘기 때문.

### 나쁜 코드 대처 방식

- **안좋은 습관**
    
    주석을 달아서 해결
    
- **개선할 방안**
    
    코드를 정리하기.
    

**이유**

표현력이 풍부하고 깔끔하며 주석이 거의 없는 코드가, 복잡하고 어수선하며 주석이 많이 달린 코드보다 훨씬 좋다.

## 코드로 의도를 표현하라!

---

1. 주석으로 표현

```java
//직원에게 복지 혜택을 받을 자격이 있는지 검사한다.
if((employee.flags & HOURLY_FLAG) && 
   (employee.age>65))
```

1. 함수로 표현

```java
if(employee.isEligibleForFullBenefits())
```

위와 같이 **주석으로 달려는 설명을 함수로 만들어 표현해도 충분**하다는 걸 알 수 있다.

## 좋은 주석

---

**명심할 거.**

어떤 주석은 필요하거나 유익하지만, “정말로 좋은 주석은, 주석을 달지 않을 방법을 찾아낸 주석이라는 사실.”

### 1. 법적인 주석

회사의 표준에 맞춰 법적인 이유로 특정 주석을 넣으라고 명시하는 경우.

- 저작권 정보
- 소유권 정보

### 2. 정보를 제공하는 주석

기본적인 정보를 주석으로 제공하면 편리하다.

- 추상 메서드가 반환할 값을 설명
    
    1번
    
    ```java
    //테스트 중인 Responder 인스턴스를 반환한다.
    protected abstract Responder responderInstance();
    ```
    
    2번
    
    ```java
    protected abstract Responder responderBeingTested();
    ```
    
    1번도 괜찮지만 2번처럼 함수명을 변경하면 주석이 필요없어짐.
    
- 정규표현식이 시각과 날짜를 뜻한다고 설명
    
    ```java
    //kk:mm:ss EEE,MMM dd,yyyy 형식이다
    Pattern timeMatcher = Pattern.compile("\\d*:\\d*:\\d*: \\w*:\\w* \\d*,\\d*");
    ```
    
    위의 코드를 시각과 날짜를 변환하는 클래스를 만들어 코드를 옮겨주면 더 좋고 더 깔끔함.
    

### 3. 의도를 설명하는 주석

구현을 이해하게 도와주는 선을 넘어 결정에 깔린 의도까지 설명한다.

잘 이해가 되지 않음.!(p.72)

```java
public int compareTo(Object o){
     if(o instanceof WikiPagePath){
         ....
     }
     // ~~한 경우에는 우선순위가 높다.
     return 1;
 }
```

```java
public void testConcurrentAddWidgets() {
     ...
     // 스레드를 대량 생성하는 방법으로 경쟁 조건을 만든다.
     for (int i = 0; i < 25000; i++){
         new Thread(widgetBuilderThread).start();
     }
     ...
 }
```

### 4. 의미를 명료하게 밝히는 주석

모호한 인수나 반환값은 그 의미를 읽기 좋게 표현하면 이해하기 쉬워진다. 

인수나 반환값 자체를 명확히 만들면 더 좋겠지만, 인수나 반환값이 표준 라이브러리나 변경하지 못하는 코드에 속한다면 의미를 명료하게 밝히는 주석이 유용하다.

```java
assertTrue(a.compareTo(a) == 0) //a == a
assertTrue(a.compareTo(b) != 0) //a != b
assertTrue(a.compareTo(ab) == 0) //ab == ab
assertTrue(a.compareTo(b) != -1) //a < b
```

**주의.**

주석이 올바른지 검증하기 쉽지 않음.
따라서 위와 같은 주석을 달 때는 더 나은 방법이 없는지 고민하고 정확히 달도록 해야한다.

### 5. 결과를 경고하는 주석

다른 프로그래머에게 결과를 경고할 목적으로 주석을 사용한다.

- 특정 테스트 케이스를 꺼야 하는 이유를 설명하는 주석
    
    ```java
    // 여유 시간이 충분하지 않다면 실행하지 마십시오.
    public void _testWithReallyBigFile(){
      writeLinesToFile(100000000);
      .....
    }
    ```
    
    요즘은 **@Ignore** 속성을 이용해서 테스트 케이스를 꺼버린다고함.
    

### 6. TODO 주석

‘앞으로 할 일’을 //TODO 주석으로 남기면 편하다.

- 함수를 구현하지 않은 이유와 미래 모습을 //TODO 주석으로 설명
    
    ```java
    //TODO: MdM 현재 필요하지 않다
    //체크아웃 모델을 도입하면 함수가 필요없다.
    protected VersionInfo makeVersion() throws Exception{
        return null;
    }
    ```
    

주기적으로 TODO 주석을 점검해 없애도 괜찮은 주석은 없애는걸 권한다.

### 7. 중요성을 강조하는 주석

자칫 대수롭지 않다고 여겨질 뭔가의 중요성을 강조하기 위해 주석을 사용한다.

```java
String listItemContent = match.group(3).trim()
//여기서 trim은 매우 중요하다. trim 함수는 문자열에서 시작 공백을 제거한다.
//문자열에 시작 공백이 있으면 다른 문자열로 인식되기 때문이다.
...
```

### 8. 공개 API에서 Javadocs

공개 API를 구현한다면 반드시 훌륭한 Javadocs를 작성한다.

- Javadoc란?
    
    간단히 말하면 문서같은 주석?
    
    ```java
    /**
    * 내용
    */
    ```
    
    위와 같은 형식을 가지고 있다.
    
    사전적 정의로는 **JAVA 소스코드에서 API 문서를 html 태그 형식으로 작성하게 해주는 도구이다.**
    
    참고 : [https://velog.io/@ming/JavaDoc-주석-알고쓰자](https://velog.io/@ming/JavaDoc-%EC%A3%BC%EC%84%9D-%EC%95%8C%EA%B3%A0%EC%93%B0%EC%9E%90)
    

## 나쁜 주석

---

### 1. 주절거리는 주석

이해가 안 되어 다른 모듈까지 뒤져야 하는 주석은 독자와 제대로 소통하지 못하는 주석이다.

→ 개발자만 이해하는 주석

```java
public void loadProperties() {
    try {
        loadedProperties.load(propertiesStream);
    } catch (IOException e) {
        // 속성 파일이 없다면 기본 값을 모두 메모리로 읽어 들였다는 의미다.
    }
}
```

### 2. 같은 이야기를 중복하는 주석

```java
// this.closed가 true일 때 반환되는 유틸
// 타임아웃에 도달하면 예외를 던짐
public synchronized void waitForClose(final long millis) throws Exception {
    if(!closed) {
        wait(millis);
        if(!closed) throw new Exception();
    }
}
```

주석이 코드 내용을 그대로 중복함 → 주석이 코드보다 더 많은 정보를 제공하지 못함.

### 3. 오해할 여지가 있는 주석

주석에 잘못된 정보가 적혀있을 수 있다.

### 4. 의무적으로 다는 주석

모든 함수나 모든 변수에 주석을 다는 행위는 코드를 복잡하게 만들며, 거짓말할 가능성을 높이며, 잘못된 정보를 제공할 여지만 만든다. → **이런 주석은 아무 가치가 없음.**

```java
/**
 * 
 * @param title CD 제목
 * @param author CD 저자             
 */
public void addCD(String title, String author) {
    CD cd = new CD();
    cd.title = title;
    cd.author = author;
    cds.add(cd);
}
```

### 5. 이력을 기록하는 주석

**소스 코드 관리 시스템(Git)**을 사용해서 이런 주석은 완전히 제거하는게 좋다.

### 6. 있으나 마나 한 주석

너무 당연한 사실을 언급하며 새로운 정보를 제공하지 못하는 주석.

```java
// 기본 생성자
protected Member() {}

// 월 중 일자
private int dayOfMonth;
```
![주석](https://user-images.githubusercontent.com/43058016/175814666-a0e20b10-bc20-4ca0-94be-b4e4fdb58dc8.png)

위 예시를 보고 많이 뜨끔했음. ㅎㅎ..

**부작용**

위와 같은 주석을 많이 사용하면 개발자는 주석을 무시하는 습관에 빠지게 될 수 있음.

### 7. 무서운 잡음

목적이 없으면 주석은 가치가 없음.

### 8. 함수나 변수로 표현할 수 있다면 주석을 달지 마라

예시로 설명

```java
// 전역목록 <smodule>에 속하는 모듈이 우리가 속한 하위 시스템에 의존하는가?
if(smodule.getDependSubsystems().contains(subSysMod.getSubSystem()))
```

vs

```java
ArrayList moduleDependees = smodule.getDependSubsystems();
String ourSubSystem = subSysMod.getSubSystem();
if (moduleDependees.contains(ourSubSystem))
```

아래처럼 주석이 필요하지 않도록 코드를 개선하는 편이 더 좋음.

### 9. 위치를 표시하는 주석

아주 드물게 사용하는 편이 좋음.

### 10. 닫는 괄호에 다는 주석

닫는 괄호에 주석을 달아야겠다는 생각이 든다면 대신에 **함수(크기)를 줄이려 시도**하는게 좋다.

```java
try{
  while((line = in.readLine) != null){
    lineCount++;
    charCount += line.length();
    String words[] = line.split("a");
    wordCount += words.length;
  } //while
  System.out.println("wow");
  System.out.println("wow");
  System.out.println("wow");
} //try
```

### 11. 공로를 돌리거나 저자를 표시하는 주석

**소스 코드 관리 시스템**을 사용하자.!

### 12. 주석으로 처리한 코드

주석으로 처리한 코드는 다른 사람들이 지우기를 주저함 → 쓸모 없는 코드가 점차 쌓이게 됨.

따라서 이런 주석은 절대 쓰지 않도록 하자. 또한, **소스 코드 관리 시스템**을 사용하기.

### 13. HTML 주석

저자 : HTML 주석은 **혐오** 그 자체다.

### 14. 전역 정보

주석을 달아야 한다면 근처에 있는 코드만 기술해라.

**이유.**

전역변수 설정 코드가 변경되어도 주석으로된 전역변수는 그대로 일 수 있음.

### 15. 너무 많은 정보

불필요한 정보는 쓰지말아라.

### 16. 모호한 관계

**주석**과 **주석이 설명하는 코드**는 둘 사이 관계가 명백해야 한다.

→ **읽는 사람이 알아들어야해서**

### 17. 함수 헤더

함수 헤더 < **이름 잘 지은 함수**

### 18. 비공개 코드에서 Javadocs

공개하지 않을 코드라면 Javadocs는 쓸모가 없다.
