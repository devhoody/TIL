# 스코프

## 스코프란?
    - 변수에 접근 가능한 범위
- 스코프가 존재하는 이유
    - 메모리 누수 방지
    - 코드 복잡성 증가

scope1

```java
public class scope1 {
    public static void main(String[] args) {
        int m =10; // m 생존 시작
        if(true){
            int x = 20; // x 생존 시작
            System.out.println("if m = " + m);
            System.out.println("if x = " + x);
        } // x 생존 종료
//        System.out.println("main x = " + x); // 에러 발생
        System.out.println("main m = " + m);

    }
}

```

- `int m`
    - **main() 블록 안에서 선언**되었다. 즉, main{} 안에서만 생존한다.
    - main() 내부 블록에 있는 if {}에서도 사용 가능하다. 즉, **main{}블록 안에서는 어디서든 사용이 가능하다.**
- `int x`
    - **조건문 if 블록 안에서 선언**되었다. 즉, if{} 안에서만 생존한다.
    - if 블록 밖에서 사용할 경우 **컴파일 오류**가 발생한다. → **“connot find symbol”** = 변수를 찾을 수 없다.
        
        <img width="244" alt="3" src="https://github.com/devhoody/allmap/assets/124743189/9c1d0a75-a4f8-49c0-8d03-ca99062c1809">

        

**Scope2**

```java
public class Scope2 {
    public static void main(String[] args) {
        int m = 10; // main 내 선언
        for (int i=0; i<2; i++){ // i : for문 내 생존
            System.out.println("for m = " + m);
            System.out.println("for i = " + i);
        }

        System.out.println("m = "+ m );
//        System.out.println("i = "+ i ); // “connot find symbol” 런타임 오류 발생
    }
}

```

- 위의 조건문과 마찬가지로 int i 는 for문 내에 선언되어 **for문 블록 내에서만 사용이 가능하다.**

## 스코프가 존재하는 이유

아래의 코드를 살펴보자

**Scope3_1** 

```java
 public class Scope3_1 {
     public static void main(String[] args) {
         int m = 10;
         int temp = 0;
         if (m > 0) {
							temp = m * 2;
	            System.out.println("temp = " + temp);
         }
         System.out.println("m = " + m);
     }
}
```

- `int temp`가 main() 에서 선언되었다. 하지만, temp는 if문 내에서만 사용하고 다른 곳에서는 사용되지 않는다. → 이렇게 되면 다음과 같은 문제가 발생한다.
    - **비효율적인 메모리 사용**
        - **temp는 if문에서만 사용되었지만, main 메소드가 종료될 때까지 계속 메모리에 저장되어있다.** 이는 불필요한 메모리 사용을 초래한다. 만약, if문 내에서 선언되었다면, if문이 종료됨과 동시에 temp는 메모리에서 제거되므로 더 효율적인 메모리 사용이 가능하다.
    - **코드 복잡성 증가**
        - 만약 main함수가 길어져, temp 변수가 필요할 경우, if문 안에서 선언되었다면, temp를 전혀 신경쓰지 않아도 된다. 누군가가 temp 변수를 사용할 경우, 해당 문제를 신경쓰지 않아도 되니 코드 복잡성을 줄일 수 있다.

**Scope3_2**

```java
 public class Scope3_2 {
     public static void main(String[] args) {
         int m = 10;
         if (m > 0) {
             int temp = m * 2;
             System.out.println("temp = " + temp);
         }
         System.out.println("m = " + m);
     }
}
```

### while vs for

**while**

```java
public class While2_3 {
     public static void main(String[] args) {
         int sum = 0;
         int i = 1;
         int endNum = 3;
         while (i <= endNum) {
             sum = sum + i;
             System.out.println("i=" + i + " sum=" + sum);
             i++;
         }
         //... 더 많은 코드가 존재
     }
 }
```

**For**

```java
public class For2 {
     public static void main(String[] args) {
         int sum = 0;
         int endNum = 3;
         for (int i = 1; i <= endNum; i++) {
             sum = sum + i;
             System.out.println("i=" + i + " sum=" + sum);
         }
//... 아래에 더 많은 코드들이 있다고 가정 }
}
```

- 변수의 스코프 관점에서 카운터 변수인 i를 살펴보자
    - while의 경우, main 함수 내에 변수 i를 선언했다. while문이 끝나고 i가 메모리에 남아있으므로, **불필요한 메모리 사용을 초래한다.**
    - For의 경우, for문 내에서 i를 선언했다. for문이 끝나면, 자동으로 변수 i도 메모리에서 제거되므로 **효율적인 메모리 사용이 가능하다.**

### 정리

- 이처럼 적절한 스코프를 고려하여 변수를 선언한다면, 불필요한 메모리 사용이나 코드 복잡성을 줄일 수 있다.
- 그러므로 꼭 필요한 곳에서 범위를 한정하는 습관을 들이자.