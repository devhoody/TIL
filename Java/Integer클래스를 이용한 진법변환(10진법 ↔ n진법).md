# Integer클래스를 이용한 진법변환(10진법 ↔ n진법)

## 10진수 → n진수

**변환전 확인할 것!**

10진수를 제외한 나머지 진수는 모두 `String`으로 받아야 한다. 왜냐하면 자바에서 int는 모두 10진수로 인식하기 때문이다.

**사용 메소드**

- Integer.toBinaryString()
- Integer.toString()

**테스트코드**

```java
public class Test {
    public static void main(String[] args) {
        int a = 30;

        System.out.println("10진수 -> 2진수");
        System.out.println(Integer.toBinaryString(a));
        System.out.println(Integer.toString(a,2));

        System.out.println("10진수 -> 3진수");
        System.out.println(Integer.toString(a,3));

        System.out.println("10진수 -> 4진수");
        System.out.println(Integer.toString(a,4));

        System.out.println("10진수 -> 5진수");
        System.out.println(Integer.toString(a,5));

        System.out.println("10진수 -> 6진수");
        System.out.println(Integer.toString(a,6));

        System.out.println("10진수 -> 7진수");
        System.out.println(Integer.toString(a,7));

        System.out.println("10진수 -> 8진수");
        System.out.println(Integer.toOctalString(a));
        System.out.println(Integer.toString(a,8));

        System.out.println("10진수 -> 16진수");
        System.out.println(Integer.toString(a,16));
        System.out.println(Integer.toHexString(a));
    }
}
```

- 결과
    
    <img width="139" alt="1" src="https://github.com/devhoody/allmap/assets/124743189/63e042f7-98ae-415c-b9ca-4805c80551fc">
    

# 2진수 → 10진수

**변환전 확인할 것!**

String 타입으로 받은 진수 값`(s)`들을 int로 변환한다. → Integer.parseInt(String s, int radix)

**테스트코드**

```java
public class Test {
    public static void main(String[] args) {
        // 10진법 a 값 : 30
        String a = "11110";

        System.out.println("2진수 -> 10진수");
        System.out.println(Integer.parseInt(a,2));

        System.out.println("3진수 -> 10진수");
        System.out.println(Integer.parseInt(a,3));

        System.out.println("4진수 -> 10진수");
        System.out.println(Integer.parseInt(a,4));

        System.out.println("5진수 -> 10진수");
        System.out.println(Integer.parseInt(a,5));

        System.out.println("6진수 -> 10진수");
        System.out.println(Integer.parseInt(a,6));

        System.out.println("7진수 -> 10진수");
        System.out.println(Integer.parseInt(a,7));

        System.out.println("8진수 -> 10진수");
        System.out.println(Integer.parseInt(a,8));

        System.out.println("16진수 -> 10진수");;
        System.out.println(Integer.parseInt(a,16));

    }
}

```

- 결과
    
    <img width="131" alt="2" src="https://github.com/devhoody/allmap/assets/124743189/1f5c7c51-8fe7-4f2b-b372-664861551f33">
