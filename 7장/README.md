- 목차

---

<aside>
💡 **깨끗한 코드와 오류 처리는 확실히 연관성이 있다.

오류 처리 코드로 인해 프로그램 논리를 이해하기 어려워진다면 깨끗한 코드라 부르기 어렵다.**

</aside>

## 오류 코드보다 예외를 사용하라

---

- **오류 플래그 설정하거나 호출자에게 오류 코드를 반환하는 방법**
    
    ```java
    public class DeviceController {
    	...
    	public void sendShutDown() {
    		DeviceHandle handle = getHandle(DEV1);
    		// 디바이스 상태를 점검한댜.
    		if (handle != DeviceHandle.INVALID) {
    			// 레코드 필드에 디바이스 상태를 저장한다.
    			retrieveDeviceRecord(handle);
    			// 디바이스가 일시정지 상태가 아니라면 종료한다.
    			if (record.getStatus() != DEVICE_SUSPENDED) {
    				pauseDevice(handle);
    				clearDeviceWorkQueue(handle);
    				closeDevice(handle);
    			} else {
    				logger.log("Device suspended. Unable to shut down");
    			}
    		} else {
    			logger.log("Invalid handle for: " + DEV1.toString());
    		}
    	}
    	...
    }
    ```
    
    - 호출자 코드가 복잡해짐.
    - 오류 발생하면 예외를 던지는 편이 나음. → 코드 깔끔해지고 논리와 오류 처리 코드가 뒤섞이지 않음.
- **예외 사용**
    
    ```java
    public class DeviceController {
    
    	...
    
    	public void sendShutDown() {
    		try {
    			tryToShutDown();
    		}
    		catch (DeviceShutDownError e) {
    			logger.log(e);
    		}
    	}
    
    	private void tryToShutDown() {
    		DeviceHandle handle = getHandle(DEV1);
    		DeviceRecord record = retrieveDeviceRecord(handle);
    
    		pauseDevice(handle);
    		clearDeviceWorkQueue(handle);
    		closeDevice(handle);
    	}
    
    	private DeviceHandle getHandle(DeviceId id) {
    		...
    		throw new DeviceShutDownError("Invalid handle for: " + id.toString());
    		...
    	}
    	
    	...
    
    }
    ```
    
    - 디바이스 종료하는 알고리즘과 오류 처리하는 알고리즘 분리.
    ⇒ 깔끔 + 코드 품질 좋아짐.

## Try-Catch-Finally 문부터 작성하라.

---

> try 블록은 트랜잭션과 비슷하다.
> 
- try 블록에서는 무슨 일이 생기든지 catch 블록은 프로그램 상태를 일관성 있게 유지해야 한다.
- 호출자가 기대하는 상태를 정의하기 쉬워진다.

[트랜잭션 특징](https://velog.io/@vector9999/DB-%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98%EC%9D%98-4%EA%B0%80%EC%A7%80-%ED%8A%B9%EC%84%B1ACID)

- **파일이 없으면 예외를 던지는지 알아보는 단위 테스트**
    
    ```java
    @Test(expected = StorageException.class)
     public void retrieveSectionShouldThrowOnInvalidFileName() {
         sectionStore.retrieveSection("invalid - file");
     }
    ```
    
- **예외 안던지는 코드 → 실패**
    
    ```java
    public List<RecordedGrip> retrieveSection(String sectionName) {
         // 실제로 구현할 때까지 비어 있는 더미를 반환한다.
         return new ArrayList<RecordedGrip>();
     }
    ```
    
- **잘못된 파일 접근 시도하는 코드(예외 던짐) → 성공**
    
    ```java
    public List<RecordedGrip> retrieveSection(String sectionName) {
         try {
             FileInputStream stream = new FileInputStream(sectionName);
         } catch (Exception e) {
             throw new StorageException("retrieval error", e);
         }
         return new ArrayList<RecordedGrip>();
     }
    ```
    
    - 리팩터링
    
    Exception → FileNotFoundException 으로 예외 유형을 좁힘.
    
    ```java
    public List<RecordedGrip> retrieveSection(String sectionName) {
         try {
             FileInputStream stream = new FileInputStream(sectionName);
             stream.close();
         } catch (FileNotFoundException e) {
             throw new StorageException("retrieval error", e);
         }
         return new ArrayList<RecordedGrip>();
     }
    ```
    

## 미확인 예외를 사용하라

---

### 미확인 예외 vs 확인된 예외

- **미확인 예외 (UnChecked Exception)**
    - 프로그램 로직의 오류로 인한 예외.
    - **런타임** 시점에 예외처리 여부를 확인.
    - ArrayIndexOutOfBoundsException, IllegalArgumentException, NullPointerException
- **확인된 예외 (Checked Exception)**
    - 프로그램 제어 밖에 있는 예외.
    - **컴파일** 시점에 예외처리 여부를 확인.
    - FileNotFoundException, SQLException, IOException, ClassNotFoundException

참고한 사이트 : [https://velog.io/@sangmin7648/throws는-언제-사용해야할까](https://velog.io/@sangmin7648/throws%EB%8A%94-%EC%96%B8%EC%A0%9C-%EC%82%AC%EC%9A%A9%ED%95%B4%EC%95%BC%ED%95%A0%EA%B9%8C)

<aside>
💡 **확인된 예외는 OCP를 위반한다.**

</aside>

- **OCP란?**
    
    소프트웨어 엔티티(클래스, 모듈, 함수 등)는 확장에 대해서는 열려 있어야 하지만 변경에 대해서는 닫혀 있어야 한다.
    
    **즉. OCP는 해당 클래스의 기존 동작을 변경하지 않고 클래스의 동작을 확장하는 것을 목표로 함.**
    
- 확인된 예외를 사용하면 결과적으로 최**하위 단계에서 최상위 단계까지 연쇄적인 수정**이 일어남.
- throws 경로에 위치하는 모든 함수가 최하위 함수에서 던지는 예외를 알아야 하므로 **캡슐화가 깨짐.**

## 예외에 의미를 제공하라

---

<aside>
💡 **오류가 발생한 원인과 위치를 찾기 쉽게 전후 상황을 충분히 덧붙이기.**

</aside>

- 자바에서 호출 스택 제공하지만 부족함.
- 오류 메시지에 실패한 연산 이름과 실패 유형과 같은 정보를 담아 예외와 함께 던지기.

## 호출자를 고려해 예외 클래스를 정의하라

---

<aside>
💡 오류를 정의할 때 프로그래머에게 가장 중요한 관심사는 **오류를 잡아내는 방법**이 되어야 한다.

</aside>

- **외부 라이브러리가 던질 예외를 모두 잡은 코드**
    
    ```java
    ACMEPort port = new ACMEPort(12);
    
     try {
         port.open();
     } catch (DeviceResponseException e) {
         reportPortError(e);
         logger.log("Device response exception", e);
     } catch (ATM1212UnlockedException e) {
         reportPortError(e);
         logger.log("Unlock exception", e);
     } catch (GMXError e) {
         reportPortError(e);
         logger.log("Device response exception");
     } finally {
         ...
     }
    ```
    
- **호출하는 라이브러리 API를 감싸면서 예외 유형 하나를 반환하는 코드**
    
    ```java
    LocalPort port = new LocalPort(12);
     try {
         port.open();
     } catch (PortDeviceFailure e) {
         reportError(e);
         logger.log(e.getMessage(), e);
     } finally {
         ...
     }
    ```
    
    ```java
    public class LocalPort {
         private ACMEPort innerPort;
    
         public LocalPort(int portNumber) {
             innerPort = new ACMEPort(portNumber);
         }
    
         public void open() {
             try {
                 innerPort.open();
             } catch (DeviceResponseException e) {
                 throw new PortDeviceFailure(e);
             } catch (ATM1212UnlockedException e) {
                 throw new PortDeviceFailure(e);
             } catch (GMXError e) {
                 throw new PortDeviceFailure(e);
             }
         }
         ...
     }
    ```
    

- 외부 API를 사용할 때는 감싸기 기법이 최선이다.
- 감싸기 기법을 사용하면 해당 API를 설계한 방식에 발목 잡히지 않음. (의존 X)

## 정상 흐름을 정의하라

---

<aside>
💡 오류 처리로 인한 중단이 때로는 적합하지 않은 때도 있다.

</aside>

- **특수 사례 패턴 적용 전**
    
    ```java
    try {
    	MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
    	m_total += expenses.getTotal();
    } catch(MealExpensesNotFound e) {
    	m_total += getMealPerDiem();
    }
    ```
    
- **특수 사례 패턴 적용**
    
    ```java
    MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
    m_total += expenses.getTotal();
    ```
    
    ```java
    public class PerDiemMealExpenses implements MealExpenses {
    	public int getTotal() {
    		// 기본값으로 일일 기본 식비를 반환한다.
    	}
    }
    ```
    
    - getTotal 함수에 예외가 절대 반환할 수 없도록 코드를 수정
    - 클래스를 만들거나 객체를 조작해 예외적인 상황을 캡슐화해서 처리.

## null을 반환하지 마라

---

<aside>
💡 **null을 반환하는 습관은 나쁘다.**

</aside>

- 호출자에게 null을 체크할 의무를 줌
- null 체크를 빼먹으면 NullPointerException 발생할 수 있음

- **null 체크하는 코드**
    
    ```java
    List<Employee> employees = getEmployees();
    if(employees != null) {
    	for(Employee e : employees) {
    		totalPay += e.getPay();
    	}
    }
    ```
    
- **null 체크안해도 되는 코드 (특수 사례 객체 반환)**
    
    ```java
    List<Employee> employees = getEmployees();
    for(Employee e : employees) {
    	totalPay += e.getPay();
    }
    
    public List<Employee> getEmployees() {
    	if (..직원이 없다면..)
    		return Collections.emptyList();
    }
    ```
    
    - 애초에 null 반환하지 않으므로 null 체크 안해도 됨.
    - 코드도 깔끔해지고 NullPointerException 발생할 가능성 줄어듬

## null을 전달하지 마라

---

<aside>
💡 null 반환하는 방식도 나쁘지만 **null 전달하는 방식은 더 나쁘다.**

</aside>

- 애초에 null을 전달하지 못하게 해서 null 처리하는 것도 없게 하는게 좋음.

## 결론

---

<aside>
💡 **깨끗한 코드는 읽기도 좋아야 하지만 안정성도 높아야 한다.

오류 처리를 프로그램 논리와 분리해 튼튼하고 깨끗한 코드를 작성하면 코드 유지보수성도 높아진다.**

</aside>
