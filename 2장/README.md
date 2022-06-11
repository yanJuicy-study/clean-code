# 2장 의미 있는 이름

### 의도를 분명히 밝혀라

좋은 이름을 지으려면 시간이 걸리지만 좋은 이름으로 절약하는 시간이 훨씬 더 많다.

변수나 함수, 클래스 이름은 다음 질문에 모두 답해야 한다.

- 변수의 존재 이유는?
- 수행 기능은?
- 사용 방법은?

주석이 필요하다면 의도를 분명히 드러내지 못했다는 말이다.

![image](https://user-images.githubusercontent.com/43159295/173191584-44ef3641-7830-4419-8f90-c77fe47ca17e.png)

‘현업에서는 주석을 통해 의미를 나타내는 경우도 있다고 한다.’

```java
int d; // 경과 시간 (단위 : 날짜)
```

이름 d는 아무 의미도 드러나지 않는다.

다음과 같이 의미가 드러난게 이름을 정한다.

```java
int elapesedTimeInDays;
int daysSinceCreation;
int daysSinceModification;
int fileAgeInDays;
```

다음 코드는 무엇을 할까? 코드가 하는 일을 짐작하기 어렵다.

```java
public List<int[]> getThem() {
	List<int[]> list1 = new ArrayList<int[]> ();

	for (int[] x: theList)
		if (x[0] == 4)
			list1.add(x);
	return list1;
}
```

문제는 코드의 단순성이 아니라 코드의 함축성이다. 코드 맥락이 코드 자체에 명시적으로 드러나지 않는다. 위 코드는 독자가 다음과 같은 정보를 안다고 가정한다.

1. theList에 무엇이 들었는가?
2. theList에서 0번째 값이 어째서 중요한가?
3. 값 4는 무슨 의미인가?
4. 함수가 반환하는 리스트 list1을 어떻게 사용하는가?

위 코드를 지뢰찾기 코드로 가정하면, theList가 게임판이라는 사실을 안다.

그러면 theList를 gameBoard로 바꾸자.

게임판에서 각 칸은 단순 배열로 표현한다.

- 배열에서 0번째 값은 칸 상태를 뜻한다.

- 값 4는 깃발이 꽂힌 상태를 가리킨다.

int 배열은 칸을 의미하므로 Cell이라는 클래스로 만든다.

```java
public List<Cell> getFlaggedCells() {
	List<Cell> fleaggedCells = new ArrayList<int[]> ();

	for (Cell cell: gameBoard) {
		if (cell.isFlagged()) {
			fleaggedCells.add(cell);
		}
	}
	return flaggedCells;
}
```

코드의 단순성은 변하지 않았지만 코드는 더욱 명확해졌다.

![image](https://user-images.githubusercontent.com/43159295/173191608-10b6e111-7e2f-470e-98c8-73562c7bbe94.png)

![image](https://user-images.githubusercontent.com/43159295/173191636-7096667b-559b-42e0-b628-e51a7d2b4a7b.png)

### 그릇된 정보를 피하라

나름대로 널리 쓰이는 의미가 있는 단어를 다른 의미로 사용하면 안 된다.

- hp, aix, sco는 유닉스 플랫폼을 가리키는 이름이기 때문에 적합하지 않다.
- 컨테이너로 List를 사용하지 않는데, accountList 같이 사용하면 그릇된 정보를 제공한다.
  - accountGroup, bunchOfAcoounts, Accounts라 명명한다.

서로 흡사한 이름을 사용하지 않도록 주의한다. 차이를 알기 어렵다

- XYZControllerForEfficientHandlingOfStrings
- XYZControllerForEfficientStroageOfStrings

유사한 개념은 유사한 표기법을 사용한다.

- 일관성이 떨어지는 표기법은 그릇된 정보다.
- 대부분 프로그래머는 자동완성 기능을 단순히 이름만 보고 사용한다.

정말 끔찍한 옛는 소문자 L이나 대문자 O 변수다.

```java
int a = 1;
if (O == 1)
	a = O1;
else
	l = 01;
```

![image](https://user-images.githubusercontent.com/43159295/173191651-aafff678-96b5-450e-bc3b-56ba1dd3be6a.png)

![image](https://user-images.githubusercontent.com/43159295/173191662-81962453-2928-433f-af1b-0f8f0f16d195.png)

### 의미 있게 구분하라

컴파일러나 인터프리터만 통과하려는 생각으로 코드를 구현하는 프로그래머는 스스로 문제를 일으킨다.

- 동일한 범위 안에서는 다른 두 개념에 같은 이름을 사용하지 못한다.
- 어떤 프로그래머는 철자를 살짝 바꿨다가 나중에 철자 오류를 고치는 순간 컴파일이 불가능한 상황에 빠진다.

컴파일러를 통과하더라도 연속된 숫자를 덧붙이거나 불용어를 추가하는 방식은 적절하지 못하다.

이름이 달라야 한다면 의미도 달라져야 한다.

```java
public void copyChars(char a1[], char a2[]) {
	for (int i = 0; i < a1.length; i++) {
		a2[i] = a1[i];
	}
}

public void copyChars(char source[], char destination[]) {
	for (int i = 0; i < a1.length; i++) {
		destination[i] = source[i];
	}
}
```

불용어는 중복이다.

- 변수 이름에 variable이라는 단어는 금물이다.
- 표 이름에 table이라는 단어도 마찬가지다.
- NameString이 Name과 다를게 없다.
- Customer와 CustomerObject의 차이는 없다.

### 발음하기 쉬운 이름을 사용하라

사람들은 단어에 익숙하다. 단어는 발음이 가능하다. 말을 처리하려고 발달한 두뇌를 활용하지 않는다면 손해다. 그러므로 발음하기 쉬운 이름을 선택한다.

발음하기 어려운 이름은 토론하기도 어렵다.

- genymdhms 단어를 쓴느 회사는 “젠 와이 엠 디 에이취 엠 에스”, “젠 야 무다 힘즈” 등 통일이 안된다.

```java
class DtaRcrd102 {
	private Date genymdhms;
	private Date modymdhms;
	private final String pszqint = "102";
}

class Customer {
	private Date generationTimestamp;
	private Date modificationTimestamp;
	private final String recordId = "102";
}
```

두 번째 코드는 지적인 대화가 가능하다.

### 검색하기 쉬운 이름을 사용하라

문자 하나를 사용하는 이름과 상수는 텍스트 코드에서 쉽게 눈에 띄지 않는다는 문제점이 있다.

- MAX_CLASS_PER_STUDENT는 찾기 쉽지만, 숫자 7은 까다롭다.
- 검색은 되었지만, 7을 사용한 의도가 다른 경우도 있다.
- e라는 문자도 변수 이름으로 적합하지 못하다.

```java
for (int j=0; j<34; j++) {
	s += (t[j]*4)/5;
}

int realDaysPerIdealDay = 4;
const int WORK_DAYS_PER_WEEK = 5;
int sum = 0;
for (int j=0; j<NUMER_OF_TASKS; j++) {
	int realTaskDays = taskEstimate[i] * realDaysPerIdealDay;
	int realTaskWeeks = (realTaskDays / WORK_DAYS_PER_WEEK);
	sum += realTaskWeeks;
}
```

아래 코드처럼 sum을 사용하면 검색이 가능하다. WORK_DAYS_PER_WEEK도 검색하기 쉽다. 만약 5를 그냥 찾으면 5가 들어가는 이름을 모두 찾은 후 의미를 분석해 원하는 상수를 가려내야 한다.

![image](https://user-images.githubusercontent.com/43159295/173191681-1dc25a9e-be53-448e-9f1d-add02566427d.png)

![image](https://user-images.githubusercontent.com/43159295/173191636-7096667b-559b-42e0-b628-e51a7d2b4a7b.png)

### 인코딩을 피하라

이름에 인코딩할 정보는 아주 많다. 유형이나 범위 정보까지 인코딩에 넣으면 그만큼 이름을 해독하기 어려워진다.

대개 새로운 개발자가 익힐 코드 양은 상당히 많다. 여기다 인코딩 언어까지 익히라는 요구는 비합리적이다.

### 자신의 기억력을 자랑하지 마라

독자가 코드를 읽으면서 변수 이름을 자신이 아는 이름으로 변환해야 한다면 그 변수 이름은 바람직하지 못하다.

문자 하나만 사용하는 변수 이름은 문제가 있다.

- 루프에서 반복 횟수를 세는 i,j,k는 괜찮다.
- 루프 범위가 아주 작고 다른 이름과 충돌하지 않을 때만 괜찮다.
- 그 외에는 대부분 적절하지 못하다.
- 최악은 이미 a와 b를 사용하므로 c를 선택하는 논리다.

전문가는 자신의 능력을 좋은 방향으로 사용해 남들이 이해하는 코드를 내놓는다.

### 클래스 이름

클래스 이름과 객체 이름은 명사구가 적합하다.

- Customer, WikiPage, Account, AddresssParser 등이 좋은 예
- Manager, Processor, Data, Info 등과 같은 단어는 피한다.

동사는 사용하지 않는다.

### 메서드 이름

메서드 이름은 동사나 동사구가 적합하다.

postPayment, deletePage, save 등이 좋은 예

접근자, 변경자, 조건자는 표준에 따라 값 앞에 get, set, is를 붙인다.

### 한 개념에 한 단어를 사용하라

추상적인 개념 하나에 단어 하나를 선택해 이를 고수한다.

똑같은 메서드를 클래스마다 fetch, retrieve, get으로 제각각 부르면 혼란스럽다.

어느 클래스에서 어느 이름을 썼는지 기억하기 어렵다.

- controller, manager, driver를 섞어 쓰면 혼란스럽다.
- DeviceManger와 ProtocolController는 근본적으로 어떻게 다른가?

일관성 있는 어휘는 코드를 사용할 프로그래머가 반갑게 여길 선물이다.

### 말장난을 하지 마라

한 단어를 두 가지 목적으로 사용하지 마라. 다른 개념에 같은 단어를 사용한다면 말장난에 불과하다.

- 여러 클래스에 add라는 메서드가 생겼다. 모든 add 메서드의 매개변수와 반환값이 의미적으로 똑같다면 문제가 없다.
- 같은 맥락이 아닌데도 ‘일관성’을 고려해 add라는 단어를 선택한다.
  - 기존 add 메서든는 두 개를 더하거나 이어서 새로운 값을 만든다.
  - 새로 작성한 add는 집합에 값 하나를 추가한다.
  - 새 메서드는 기존 add 메서드와 맥락이 다르다. 그러므로 insert나 append라는 이름이 적당하다.

프로그래머는 코드를 최대한 이해하기 쉽게 짜야 한다. 집중적인 탐구가 필요한 코드가 아니라 대충 훑어봐도 이해할 코드 작성이 목표다.

### 해법 영역에서 가져온 이름을 사용하라

코드를 읽는 사람도 프로그래머다. 그러므로 전산 용어, 알고리즘 이름, 패턴 이름, 수학 용어 등을 사용해도 괜찮다.

기술 개념에는 기술 이름이 가장 적합한 선택이다.

- VISITOR 패턴에 익숙하면 AccountVisitor 이름이 적절하다
- JobQueue를 사용하려면 그대로 사용한다.

### 문제 영역에서 가져온 이름을 사용하라

적절한 프로그래머 용어가 없다면 문제 영역에서 이름을 가져온다.

그러면 코드를 보수하는 프로그래머가 분야 전문가에게 의미를 물어 파악할 수 있다.

문제 영역 개념과 관련이 깊은 코드라면 문제 영역에서 이름을 가져와야 한다.

### 의미 있는 맥락을 추가하라

firstName, lastName, street, houseNumber, city, state, zipcode 라는 변수가 있을 때 state의 의미가 주소라는 사실을 금방 알아챈다.

하지만 어느 메서드가 state라는 변수 하나만 사용하면, state가 주소 일부라는 사실을 알기 힘들다.

이 때 addr라는 접두어를 추가해 addrState라 쓰면 맥락이 좀 더 분명해진다.

또는 Address라는 클래스를 생성하면 더 좋다. 그러면 변수가 좀 더 큰 개념에 속한다는 사실이 컴파일러에게도 분명해진다.

```java
private void printGuessStatistics(char candidate, int count) {
	String number;
	String verb;
	String pluralModifier;

	if (count == 0) {
		number = "no";
		verb = "are";
		pluralModifier = "s";
	} else if (count == 1) {
		number = "1";
		verb = "is";
		pluralModifier = "";
	} else {
		number = Integer.toString(count);
		verb = "are";
		pluralModifier = "s";
	}
	String guessMessage = String.format(
		"There %s %s %s%s", verb, number, candidate, pluralModifier
	);
	print(guessMessage);
}
```

위 코드는 함수가 좀 길고 number, verb, pluralModifier의 의미를 함수를 다 읽어야 맥락을 파악할 수 있다.

위 세 변수를 가지는 GuessStatisticsMessage라는 클래스를 만들어서 맥락을 개선하면 함수를 쪼개기 쉬워지고 알고리즘도 더 명확해진다.
