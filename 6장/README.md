# 6장 객체와 자료구조

남들이 변수에 의존하지 않도록 변수는 private로 정의한다.

그렇다면 왜 get set 함수를 당연하게 public 해 변수를 외부에 노출시킬까?

### 자료 추상화

**변수 사이에 함수라는 계층을 넣는다고 구현이 저절로 감춰지진 않는다.**

**구현을 감추려면 추상화가 필요하다.**

인터페이스나 get/set 함수만으로는 추상화가 이뤄지지않는다. 개발자는자료를 세세하게 공개하기 보다는 추상적인 개념으로 표현하는 편이 좋다.

- 구체적인 Point 클래스 (구현을 외부로 노출)

```java
public class Point {
  public double x;
  public double y;
}
```

- 추상적인 Point 클래스 (구현을 완전히 숨김)

```java
public interface Point {
  double getX();
  double getY();
  void setCatesian(double x, double y);
  double getR();
  double getTheta();
  void setPolar(double r, double theta);
}
```

- 구체적인 Vehicle 클래스 (구현을 외부로 노출)

```java
public interface Vehicle {
  double getFuelTankCapacityInGallons();
  double getGallonsOfGasoline();
}
```

- 추상적인 Vehicle 클래스 (구현을 완전히 숨김)

```java
public interface Vehicle {
  double getPercentFuelRemaining();
}
```

### 자료/객체 비대칭

- 객체는 추상화 뒤로 자료를 숨긴 채 자료를 다루는 함수만 공개한다.
- 자료구조는 자료를 그대로 공개하며 별다른 함수는 제공하지 않는다.

절차적인 도형 클래스

```java
public class Square {
	public Point topLeft;
	public double side;
}

public class Rectangle {
	public Point topLeft;
	public double height;
	public double width;
}

public class Circle {
	public Point center;
	public double radius;
	public double width;
}

public class Geometry {
	public final double PI = 3.141592653585793;

	public double area(Object shape) throws NoSuchShapeException {
		if(shape instanceOf Square) {
			Square s = (Square)shape;
			return s.side * s.side;
		}
	else if(shape instanceOf Rectangle) {
			Rectangle r = (Rectangle)shape;
			return r.height * r.width;
		}
	else if(shape instanceOf Circle) {
			Circle c = (Circle)shape;
			return PI * c.radius * c.radius
		}
	}
}
```

Geomety 클래스는 세가지 도형 클래스를 다루며 각 도형 클래스는 간단한 자료구조이다.

아무 매서드도 제공하지 않고, 도형이 동작하는 방식은 Geomerty 클래스에서 구현한다.

만약 Geometry 클래스에 함수를 추가하고 싶다면 도형 클래스는 아무 형향도 받지 않는다. 

하지만 새 도형을 추가하고 싶다면 Gemoetry 클래스에 속한 함수를 모두 고쳐야 한다.

객체 지향적인 도형 클래스

```java
public class Square implements Shape {
	public Point topLeft;
	public double side;

	public double area() {
		return side*side;
	}
}

public class Rectangle implements Shape {
	public Point topLeft;
	public double height;
	public double width;

	public double area() {
		return height*width;
	}
}

public class Circle implements Shape {
	public Point center;
	public double radius;
	public double width;

	public double area() {
		return PI*radius*radius;
	}
}
```

객체지향적인 클래스에서는 새 도형을 추가하더라도 기존 함수에 아무런 영향이 없지만

새 함수를 추가하고싶을 떄는 도형 클래스 전부를 고쳐야 한다.

객체지향 코드에서 어려운 변경은 절차적인 코드에서 쉬우며, 반대는 객체지향 코드에서 쉽다.

### 디미터 법칙

메소드가 반환하는 객체의 메소드를 사용하면 안된다.

디미터 법칙 : 모듈은 자신이 조작하는 객체의 속사정을 몰라야 한다. 즉, 객체는 조회 함수로 내부 구조를 공개하면 안된다는 의미다.

🔍**기차 충돌**

```java
String outputDir = ctxt.getOptions().getScratchDir().getAbsolutePath();
```

위와같은 코드를 기차 충돌이라고 한다. 조잡하다고 여겨지는 방식이므로 피하는 편이 좋다.

아래와 같이 나누는 편이 좋다.

```java
Options opts = ctxt.getOptions();
File scratchDir = opts.getScratchDir();
String outputDir = scratchDir.getAbsolutePath();
```

위의 예제들이 디미터 법칙을 위반할까?

- ctxt, opts, scratchDir 이 객체라면 디미터 법칙 위반이고
- ctxt, opts, scratchDir 이 자료구조라면 내부 구조를 노출하므로 디미터 법칙이 적용되지 않는다.

```java
final String outputDir = cxtx.opts.scratchDir.absolutePath;
```

코드를 위와같이 구현했다면 디미터 법칙을 거론할 필요가 없어진다.

자료구조는 무조건 함수 없이 공개 변수만 포함하고, 객체는 비공개 변수와 공개 함수를 포함한다면 된다.

🔍**잡종 구조**

절반은 객체, 절반은 자료구조인 잡종구조

- 공개 변수, 공개 함수, 주요 함수, getter, setter 모두 섞여있는 구조다.
- 클래스, 자료 구조 양쪽에 단점만 모아 놓아 피해야할 구조다.

잡종 구조는 새로운 함수와 새로운 자료구조 둘다 추가하기 어렵다.

🔍**구조체 감추기**

```java
BufferedOutputStream bos = ctxt.createScratchFileStream(classFileName);
```

디렉토리 경로를 얻는 목적은 임시 파일을 생성하기 위함이다.

ctxt 객체가 최종 목적인 임시 파일을 생성하도록 명령하는 편이 좋다.

ctxt 객체는 내부 구조를 드러내지 않으며, 함수는 자신이 몰라야 하는 여러 객체를 담색할 필요가 없다.

### 자료 전달 객체

자료 구조체의 전형적인 형태는 공개 변수만 있고 함수가 없는 클래스다. 이런 자료구조를 DTO 라고 한다.

DTO : 공개 변수만 있고 함수가 없는 클래스

Bean : 비공개 변수와 getter, setter가 있는 클래스

활성레코드 : 공개, 비공개 변수와 getter, setter 그리고 탐색 함수가 있는 클래스
