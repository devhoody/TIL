
# static

- static이란?
    - 전역화시켜 `공유`할 수 있도록 만드는 예약어

# static변수

- 모든 클래스에서 `공용`으로 함께 사용할 수 있는 변수
- `static변수`, `정적 변수`, `클래스 변수` 라고 한다.
- static이 붙은 멤버 변수는 **힙 영역이 아닌 `메소드 영역`에서 관리한다. ⇒ 붕어빵 틀에서 관리한다라고 생각**
- 붕어빵 틀이 1개면 클래스 변수도 하나만 존재한다. (count = 1개)
    - 반면에 인스턴스인 붕어빵은 인스턴스의 수만큼 변수가 존재한다. (name = 3개)

## static을 사용하는 이유

**인스턴스 내부 변수에 카운트 저장**

- 인스턴스 메서드를 통해 생성된 객체의 수를 구하는 클래스를 만들어본다.
    
    ```java
    public class Data1 {
         public String name;
         public int count;
         public Data1(String name) {
             this.name = name;
             count++;
    } }
    ```
    
    - Data1 객체를 생성할 때마다 count 변수를 + 시켜 객체 수를 파악하고자 한다.
- main 함수에 Data1 객체를 3개 생성 시 출력이 1, 2, 3 순으로 되어야 한다.
    
    ```java
    public class DataCountMain1 {
         public static void main(String[] args) {
             Data1 data1 = new Data1("A");
             System.out.println("A count=" + data1.count);
             Data1 data2 = new Data1("B");
             System.out.println("B count=" + data2.count);
             Data1 data3 = new Data1("C");
             System.out.println("C count=" + data3.count);
         }
    }
    ```
    
    - 결과
        
        ```java
        A count=1
        B count=1
        C count=1
        ```
        
        - 하지만 결과를 보면 1 1 1로 기대된 출력인 1 2 3이 되지 않는다.
            - 왜일까? → 3개의 인스턴스 변수가 생성되면서 각각의 count 변수에 1씩 증가된다.
            - count 변수가 공유되지 않는다.

**외부 인스턴스에 카운트 저장**

- 그럼 카운트 값을 별도로 저장하는 Counter 클래스를 이용해서 count 를 증가시켜보자
- Counter 클래스
    
    ```java
    public class Counter {
         public int count;
    }
    ```
    
- Data2 클래스
    
    ```java
    public class Data2 {
         public String name;
         public Data2(String name, Counter counter) {
             this.name = name;
             counter.count++;
    } }
    ****
    ```
    
    - 여기서는 Data2 생성자에 Counter 객체를 매개변수로 하여 count를 증가시켰다.
- Main2 클래스
    
    ```java
    public class DataCountMain2 {
    	public static void main(String[] args) {
    	         Counter counter = new Counter(); // Counter 객체 생성
    
    	         Data2 data1 = new Data2("A", counter);
    	         System.out.println("A count=" + counter.count);
    	         Data2 data2 = new Data2("B", counter);
    	         System.out.println("B count=" + counter.count);
    	         Data2 data3 = new Data2("C", counter);
    	         System.out.println("C count=" + counter.count);
    	     }
    }
    ```
    
- 실행결과
    
    ```java
    A count=1
    B count=2
    C count=3
    ```
    
    - 우리가 기대했던 결과 1 2 3 이 출력된 것을 볼 수 있다.
- 하지만 여기서 불편한 점이 있다.
    1. Data2와 관련된 일인데, Counter 클래스를 따로 만들어서 사용해야한다.
    2. 생성자에 매개변수를 추가하는 등 비교적 복잡한 코드를 작성해야한다.
- static 변수를 사용하면 위의 불편한 점을 해결할 수 있다.

## static 변수 사용

- 이번에는 static 변수를 사용해서 count 변수의 수를 증가시켜본다.
- **Data3**
    
    ```java
    public class Data3 {
        public String name;
        public static int count; // static변수, 클래스변수, 정적변수
    
        public Data3(String name){
            this.name = name;
            count++;
        }
    }
    ```
    
    - count 변수에 static을 붙였다.
    - static을 붙인 변수를 static변수, 클래스 변수, 정적 변수 라고 한다.
    - Data3 객체가 생성되면 생성자에서 정적 변수 count를 1씩 증가시킨다.
- **DataCountMain3**
    
    ```java
    public class DataCountMain3 {
        public static void main(String[] args) {
            Data3 data1 = new Data3("A");
            System.out.println("A count=" + Data3.count);
    
            Data3 data2 = new Data3("B");
            System.out.println("B count=" + Data3.count);
    
            Data3 data3 = new Data3("C");
            System.out.println("C count=" + Data3.count);
        }
    }
    ```
    
    - 여기서 기존 인스턴스 메서드와의 차이점은 `Data3.count`와 같이 **클래스명에 `.(dot)`을 사용**한다는 점이다.
    - 마치 클래스 객체에 직접 접근하는 느낌이다.
- 실행결과
    
    ```java
    A count=1
    B count=2
    C count=3
    ```
    

- static이 붙은 멤버변수는 힙 영역이 아닌 메서드 영역에서 관리한다.
- Data3(”A”),Data3(”B”),Data3(”C”) 인스턴스를 생성하면 각각이 생성되면서 생성자에 의해 클래스 객체에 있는 count 변수를 1씩 증가시킨다.
- 이처럼 static을 이용하면 우리가 사용했던 Counter 클래스처럼 다른 클래스를 생성하지 않고 Data 클래스 만으로도 공유하는 변수를 만들 수 있다.

## 용어정리

```java
public class Data3 {
     public String name;
     public static int count; //static
}
```

- name과 count는 둘 다 멤버변수이다.

### 멤버 변수의 종류

- 인스턴스 변수 : static이 붙지 않은 변수 → name
    - static이 붙지 않은 변수는 인스턴스르 생성해야 사용할 수 있고, 인스턴스에 소속되어있다.
    - 인스턴스가 생성될 때마다 새로 만들어진다.
- 클래스 변수 : static이 붙은 변수 → count
    - 인스턴스와 무관하게 클래스에 바로 접근해서 사용할 수 있다. → `Data3.count`
    - 클래스 자체에 소속
    - 클래스 변수는 자바 프로그램이 실행될 때 딱 1개가 만들어진다.

### 변수와 생명주기

- **지역 변수**
    - 지역 변수는 스택 영역에서 관리. 메서드가 종료되면 스택 프레임이 제거되는데 이때 해당 프레임 안에 있는 지역 변수도 함께 사라진다. 즉, 메서드가 종료되면 지역 변수도 종료된다.
    - 지역 변수는 3개의 변수 중 생명 주기가 가장 짧다.
- **인스턴스 변수**
    - 인스턴스에 있는 멤버 변수로 힙 영역을 사용한다.
    - 힙 영역은 사용되지 않으면 제거되는 GC(가비지 컬렉션)가 발생하기 전까지는 살아있기 때문에 지역 변수보다는 생명 주기가 길다.
- **클래스 변수**
    - 클래스 자체에 소속되어있는 변수로 메서드 영역 내 static 영역에서 관리한다.
    - 클래스 변수는 자바 프로그램이 실행되면서 JVM에 의해 클래스가 로딩될 때, 1개가 생성되고 프로그램이 종료될 때까지 살아있다.
    - 생명 주기가 가장 길다.

### 정적 변수의 접근

```java
//인스턴스를 통한 접근
Data3 data4 = new Data3("D");
System.out.println(data4.count);
//클래스를 통합 접근
System.out.println(Data3.count);
```

- **인스턴스를 통한 접근** → `data4.count`
    - 추천하지 않는다. 인스턴스를 통해 접근은 가능하나, 인스턴스 변수에 접근하는 것으로 오해할 가능성이 높다.
- **클래스를 통한 접근** → `Data3.count`
    - **정적 변수에 접근할 때는 클래스를 통해 접근하도록 하자.**

# static 메서드

- `정적 메서드`, `클래스 메서드`라 한다.
- 객체 생성없이 클래스에 바로 접근하여 메서드를 이용할 수 있다.

## static 메서드 사용방법

- static을 모든 상황에서 사용할 수 있지는 않다.
- static메서드는 static만 사용할 수 있다.
    - static → static (o), instance(x)
    - 왜?
        - instance는 객체를 생성해야만 메서드나 변수를 이용할 수 있다. 반면에, static은 객체 생성 없이 사용이 가능하기 때문에, static 메서드가 인스턴스를 사용하려면 인스턴스를 생성을 해주어야만 한다.
- 반면에 모든 곳에서 static을 사용 할 수 있다.
- **instance와 static 접근**
    
    ```java
    private int instanceValue; // instance 변수
    private static int staticValue; // static 변수
    
    public static void staticCall(){
    //        instanceValue++; // 인스턴스 변수 접근
    //        instanceMethod(); // 인스턴스 메서드 접근
        staticValue++; // 정적 변수 접근
        staticMethod(); // 정적 메서드 접근
    }
    
    public void instanceCall(){
        instanceValue++; //인스턴스 변수 접근
        instanceMethod(); //인스턴스 메서드 접근
        staticValue++; //정적 변수 접근
        staticMethod(); //정적 메서드 접근
    }
    
    // 인스턴스 메서드
    private void instanceMethod() {
        System.out.println("instanceValue=" + instanceValue);
    }
    
    // static 메서드
    private static void staticMethod() {
        System.out.println("staticValue=" + staticValue);
    }
    ```
    
    - static메서드인 staticCall()에서는 인스턴스 변수와 메서드에 접근하려면 컴파일 오류가 발생한다.
    - instance 메서드인 instanceCall()에서는 인스턴스와 static 모두에 접근이 가능하다는 것을 알 수 있다.
    

## static 메서드의 활용

- static 메서드는 주로 간단한 메서드 하나로 끝나는 유틸리티성 메서드에 주로 사용한다.
- 예를 들어 계산기처럼 숫자만으로 간단한 수식을 처리하는 메서드를 만들 수 있다.

## 용어 정리

- 멤버 메서드의 종류
    - **인스턴스 메서드** : **static이 붙지 않은** 메서드
    - **클래스 메서드** : **static이 붙은** 메서드
        - `정적 메서드`, `클래스 메서드`, `static메서드`라 한다.

## static import

- 정적 메서드를 자주 호출한다면 자동으로 import 시켜주어 보다 쉽게 작성할 수 있다.
- staticCall()을 5번 이용한다고 가정해보자.
    
    ```java
    DecoData.staticCall();
    DecoData.staticCall();
    DecoData.staticCall();
    DecoData.staticCall();
    DecoData.staticCall();
    ```
    
    - 다음처럼 DecoData를 직접 다 작성해주어야하는 번거로움이 존재하는데 이를 요약시켜주는 것이 static import이다.
    
    ```java
    import static static2.DecoData.*;
    
    staticCall();
    staticCall();
    staticCall();
    staticCall();
    staticCall();
    ```
    
    - import static을 사용함으로써 이후에는 static메서드만 작성하여 사용이 가능하다.

## main() 메서드

- 우리가 주로 사용하는 public static void main(String[] args)는 바로 static 메서드이다.
- 그럼 왜 static 메서드를 사용할까?
    - main메서드는 프로그램을 실행하는 중요한 메서드인데, 이는 객체 생성없이 프로그램을 실행하도록 하기 위해서 static을 붙여준 것이다.
- 그리고 main메서드는 static 메서드만 호출할 수 있다. 따라서 main메서드와 같은 클래스에 있는 메서드를 호출하기 위해서는 모두 static메서드로 작성해주어야 한다.
    
    ```java
    public static void main(String[] args) {
        staticPrint(); // main 메서드 -> static(o)
    		instancePrint(); // 오류 발생, main 메서드 -> instance(x)
    }
    
    static void staticPrint(){
        System.out.println("static 메서드");
    }
    
    void instancePrint(){
    		System.out.println("인스턴스 메서드");
    }
    ```