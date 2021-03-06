- λͺ©μ°¨

---

<aside>
π‘ **κΉ¨λν μ½λμ μ€λ₯ μ²λ¦¬λ νμ€ν μ°κ΄μ±μ΄ μλ€.

μ€λ₯ μ²λ¦¬ μ½λλ‘ μΈν΄ νλ‘κ·Έλ¨ λΌλ¦¬λ₯Ό μ΄ν΄νκΈ° μ΄λ €μμ§λ€λ©΄ κΉ¨λν μ½λλΌ λΆλ₯΄κΈ° μ΄λ ΅λ€.**

</aside>

## μ€λ₯ μ½λλ³΄λ€ μμΈλ₯Ό μ¬μ©νλΌ

---

- **μ€λ₯ νλκ·Έ μ€μ νκ±°λ νΈμΆμμκ² μ€λ₯ μ½λλ₯Ό λ°ννλ λ°©λ²**
    
    ```java
    public class DeviceController {
    	...
    	public void sendShutDown() {
    		DeviceHandle handle = getHandle(DEV1);
    		// λλ°μ΄μ€ μνλ₯Ό μ κ²νλ.
    		if (handle != DeviceHandle.INVALID) {
    			// λ μ½λ νλμ λλ°μ΄μ€ μνλ₯Ό μ μ₯νλ€.
    			retrieveDeviceRecord(handle);
    			// λλ°μ΄μ€κ° μΌμμ μ§ μνκ° μλλΌλ©΄ μ’λ£νλ€.
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
    
    - νΈμΆμ μ½λκ° λ³΅μ‘ν΄μ§.
    - μ€λ₯ λ°μνλ©΄ μμΈλ₯Ό λμ§λ νΈμ΄ λμ. β μ½λ κΉλν΄μ§κ³  λΌλ¦¬μ μ€λ₯ μ²λ¦¬ μ½λκ° λ€μμ΄μ§ μμ.
- **μμΈ μ¬μ©**
    
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
    
    - λλ°μ΄μ€ μ’λ£νλ μκ³ λ¦¬μ¦κ³Ό μ€λ₯ μ²λ¦¬νλ μκ³ λ¦¬μ¦ λΆλ¦¬.
    β κΉλ + μ½λ νμ§ μ’μμ§.

## Try-Catch-Finally λ¬ΈλΆν° μμ±νλΌ.

---

> try λΈλ‘μ νΈλμ­μκ³Ό λΉμ·νλ€.
> 
- try λΈλ‘μμλ λ¬΄μ¨ μΌμ΄ μκΈ°λ μ§ catch λΈλ‘μ νλ‘κ·Έλ¨ μνλ₯Ό μΌκ΄μ± μκ² μ μ§ν΄μΌ νλ€.
- νΈμΆμκ° κΈ°λνλ μνλ₯Ό μ μνκΈ° μ¬μμ§λ€.

[νΈλμ­μ νΉμ§](https://velog.io/@vector9999/DB-%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98%EC%9D%98-4%EA%B0%80%EC%A7%80-%ED%8A%B9%EC%84%B1ACID)

- **νμΌμ΄ μμΌλ©΄ μμΈλ₯Ό λμ§λμ§ μμλ³΄λ λ¨μ νμ€νΈ**
    
    ```java
    @Test(expected = StorageException.class)
     public void retrieveSectionShouldThrowOnInvalidFileName() {
         sectionStore.retrieveSection("invalid - file");
     }
    ```
    
- **μμΈ μλμ§λ μ½λ β μ€ν¨**
    
    ```java
    public List<RecordedGrip> retrieveSection(String sectionName) {
         // μ€μ λ‘ κ΅¬νν  λκΉμ§ λΉμ΄ μλ λλ―Έλ₯Ό λ°ννλ€.
         return new ArrayList<RecordedGrip>();
     }
    ```
    
- **μλͺ»λ νμΌ μ κ·Ό μλνλ μ½λ(μμΈ λμ§) β μ±κ³΅**
    
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
    
    - λ¦¬ν©ν°λ§
    
    Exception β FileNotFoundException μΌλ‘ μμΈ μ νμ μ’ν.
    
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
    

## λ―ΈνμΈ μμΈλ₯Ό μ¬μ©νλΌ

---

### λ―ΈνμΈ μμΈ vs νμΈλ μμΈ

- **λ―ΈνμΈ μμΈ (UnChecked Exception)**
    - νλ‘κ·Έλ¨ λ‘μ§μ μ€λ₯λ‘ μΈν μμΈ.
    - **λ°νμ** μμ μ μμΈμ²λ¦¬ μ¬λΆλ₯Ό νμΈ.
    - ArrayIndexOutOfBoundsException, IllegalArgumentException, NullPointerException
- **νμΈλ μμΈ (Checked Exception)**
    - νλ‘κ·Έλ¨ μ μ΄ λ°μ μλ μμΈ.
    - **μ»΄νμΌ** μμ μ μμΈμ²λ¦¬ μ¬λΆλ₯Ό νμΈ.
    - FileNotFoundException, SQLException, IOException, ClassNotFoundException

μ°Έκ³ ν μ¬μ΄νΈ : [https://velog.io/@sangmin7648/throwsλ-μΈμ -μ¬μ©ν΄μΌν κΉ](https://velog.io/@sangmin7648/throws%EB%8A%94-%EC%96%B8%EC%A0%9C-%EC%82%AC%EC%9A%A9%ED%95%B4%EC%95%BC%ED%95%A0%EA%B9%8C)

<aside>
π‘ **νμΈλ μμΈλ OCPλ₯Ό μλ°νλ€.**

</aside>

- **OCPλ?**
    
    μννΈμ¨μ΄ μν°ν°(ν΄λμ€, λͺ¨λ, ν¨μ λ±)λ νμ₯μ λν΄μλ μ΄λ € μμ΄μΌ νμ§λ§ λ³κ²½μ λν΄μλ λ«ν μμ΄μΌ νλ€.
    
    **μ¦. OCPλ ν΄λΉ ν΄λμ€μ κΈ°μ‘΄ λμμ λ³κ²½νμ§ μκ³  ν΄λμ€μ λμμ νμ₯νλ κ²μ λͺ©νλ‘ ν¨.**
    
- νμΈλ μμΈλ₯Ό μ¬μ©νλ©΄ κ²°κ³Όμ μΌλ‘ μ΅**νμ λ¨κ³μμ μ΅μμ λ¨κ³κΉμ§ μ°μμ μΈ μμ **μ΄ μΌμ΄λ¨.
- throws κ²½λ‘μ μμΉνλ λͺ¨λ  ν¨μκ° μ΅νμ ν¨μμμ λμ§λ μμΈλ₯Ό μμμΌ νλ―λ‘ **μΊ‘μνκ° κΉ¨μ§.**

## μμΈμ μλ―Έλ₯Ό μ κ³΅νλΌ

---

<aside>
π‘ **μ€λ₯κ° λ°μν μμΈκ³Ό μμΉλ₯Ό μ°ΎκΈ° μ½κ² μ ν μν©μ μΆ©λΆν λ§λΆμ΄κΈ°.**

</aside>

- μλ°μμ νΈμΆ μ€ν μ κ³΅νμ§λ§ λΆμ‘±ν¨.
- μ€λ₯ λ©μμ§μ μ€ν¨ν μ°μ° μ΄λ¦κ³Ό μ€ν¨ μ νκ³Ό κ°μ μ λ³΄λ₯Ό λ΄μ μμΈμ ν¨κ» λμ§κΈ°.

## νΈμΆμλ₯Ό κ³ λ €ν΄ μμΈ ν΄λμ€λ₯Ό μ μνλΌ

---

<aside>
π‘ μ€λ₯λ₯Ό μ μν  λ νλ‘κ·Έλλ¨Έμκ² κ°μ₯ μ€μν κ΄μ¬μ¬λ **μ€λ₯λ₯Ό μ‘μλ΄λ λ°©λ²**μ΄ λμ΄μΌ νλ€.

</aside>

- **μΈλΆ λΌμ΄λΈλ¬λ¦¬κ° λμ§ μμΈλ₯Ό λͺ¨λ μ‘μ μ½λ**
    
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
    
- **νΈμΆνλ λΌμ΄λΈλ¬λ¦¬ APIλ₯Ό κ°μΈλ©΄μ μμΈ μ ν νλλ₯Ό λ°ννλ μ½λ**
    
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
    

- μΈλΆ APIλ₯Ό μ¬μ©ν  λλ κ°μΈκΈ° κΈ°λ²μ΄ μ΅μ μ΄λ€.
- κ°μΈκΈ° κΈ°λ²μ μ¬μ©νλ©΄ ν΄λΉ APIλ₯Ό μ€κ³ν λ°©μμ λ°λͺ© μ‘νμ§ μμ. (μμ‘΄ X)

## μ μ νλ¦μ μ μνλΌ

---

<aside>
π‘ μ€λ₯ μ²λ¦¬λ‘ μΈν μ€λ¨μ΄ λλ‘λ μ ν©νμ§ μμ λλ μλ€.

</aside>

- **νΉμ μ¬λ‘ ν¨ν΄ μ μ© μ **
    
    ```java
    try {
    	MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
    	m_total += expenses.getTotal();
    } catch(MealExpensesNotFound e) {
    	m_total += getMealPerDiem();
    }
    ```
    
- **νΉμ μ¬λ‘ ν¨ν΄ μ μ©**
    
    ```java
    MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
    m_total += expenses.getTotal();
    ```
    
    ```java
    public class PerDiemMealExpenses implements MealExpenses {
    	public int getTotal() {
    		// κΈ°λ³Έκ°μΌλ‘ μΌμΌ κΈ°λ³Έ μλΉλ₯Ό λ°ννλ€.
    	}
    }
    ```
    
    - getTotal ν¨μμ μμΈκ° μ λ λ°νν  μ μλλ‘ μ½λλ₯Ό μμ 
    - ν΄λμ€λ₯Ό λ§λ€κ±°λ κ°μ²΄λ₯Ό μ‘°μν΄ μμΈμ μΈ μν©μ μΊ‘μνν΄μ μ²λ¦¬.

## nullμ λ°ννμ§ λ§λΌ

---

<aside>
π‘ **nullμ λ°ννλ μ΅κ΄μ λμλ€.**

</aside>

- νΈμΆμμκ² nullμ μ²΄ν¬ν  μλ¬΄λ₯Ό μ€
- null μ²΄ν¬λ₯Ό λΉΌλ¨ΉμΌλ©΄ NullPointerException λ°μν  μ μμ

- **null μ²΄ν¬νλ μ½λ**
    
    ```java
    List<Employee> employees = getEmployees();
    if(employees != null) {
    	for(Employee e : employees) {
    		totalPay += e.getPay();
    	}
    }
    ```
    
- **null μ²΄ν¬μν΄λ λλ μ½λ (νΉμ μ¬λ‘ κ°μ²΄ λ°ν)**
    
    ```java
    List<Employee> employees = getEmployees();
    for(Employee e : employees) {
    	totalPay += e.getPay();
    }
    
    public List<Employee> getEmployees() {
    	if (..μ§μμ΄ μλ€λ©΄..)
    		return Collections.emptyList();
    }
    ```
    
    - μ μ΄μ null λ°ννμ§ μμΌλ―λ‘ null μ²΄ν¬ μν΄λ λ¨.
    - μ½λλ κΉλν΄μ§κ³  NullPointerException λ°μν  κ°λ₯μ± μ€μ΄λ¬

## nullμ μ λ¬νμ§ λ§λΌ

---

<aside>
π‘ null λ°ννλ λ°©μλ λμμ§λ§ **null μ λ¬νλ λ°©μμ λ λμλ€.**

</aside>

- μ μ΄μ nullμ μ λ¬νμ§ λͺ»νκ² ν΄μ null μ²λ¦¬νλ κ²λ μκ² νλκ² μ’μ.

## κ²°λ‘ 

---

<aside>
π‘ **κΉ¨λν μ½λλ μ½κΈ°λ μ’μμΌ νμ§λ§ μμ μ±λ λμμΌ νλ€.

μ€λ₯ μ²λ¦¬λ₯Ό νλ‘κ·Έλ¨ λΌλ¦¬μ λΆλ¦¬ν΄ νΌνΌνκ³  κΉ¨λν μ½λλ₯Ό μμ±νλ©΄ μ½λ μ μ§λ³΄μμ±λ λμμ§λ€.**

</aside>
