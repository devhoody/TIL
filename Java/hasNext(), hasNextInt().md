# hasNext(), hasNextInt()

> 백준의 단계별로 풀어보기 중 [10951번: A+B-4](https://www.acmicpc.net/problem/10951) 문제에서 나온 개념이다.

- hasNext(), hasNextInt() 메소드는 모두 `Scanner`의 메소드이다.

# hasNext()

hasNext()는 Scanner를 통해 입력된 값이 `문자열인가를 확인하는 메소드`이다.
`문자열인 경우, true`를, `문자열이 아닌 경우 false`를 반환한다.

## hasNext()를 이용한 이름 입력하기

```java
Scanner sc = new Scanner(System.in);

if(sc.hasNext()) {
    String name = sc.next();
    System.out.println("이름 : " + name);
} else {
    System.out.println("이름을 입력하세요.");
}
```

# hasNextInt()

hasNextInt()는 Scanner를 통해 입력된 값이 `정수인지 확인하는 메소드`이다.
`정수인 경우, true`를 `정수가 아닌 경우, false`를 반환한다.

## hasNextInt()를 이용한 숫자 입력하기

```java
Scanner sc = new Scanner(System.in);

if(sc.hasNextInt()) {
    int number = sc.NextInt();
    System.out.println("입력한 정수:" + number);
} else {
    System.out.println("정수를 입력하세요.");
}
```

---

### \* 백준 10951번: A+B-4 풀이

- [내가 푼 풀이](https://github.com/devhoody/Algorithm/tree/main/%EB%B0%B1%EC%A4%80/Bronze/10951.%E2%80%85A%EF%BC%8BB%E2%80%85%EF%BC%8D%E2%80%854)

```java
import java.util.Scanner;

public class Main{
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        while(sc.hasNextInt()) {
            int a = sc.nextInt();
            int b = sc.nextInt();
            System.out.println(a+b);
        }
        sc.close();
    }
}
```
