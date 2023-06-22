# 연산자 종류

| 연산자 종류 |            연산자            |
| :---------: | :--------------------------: |
| 산술 연산자 |        +, -, \*, /, %        |
| 대입 연산자 |              =               |
| 증감 연산자 |            ++, --            |
| 비교 연산자 |    <<, , <, >>, >, ==, !=    |
| 논리 연산자 |         &&, ㅣㅣ, !          |
| 비트 연산자 |     &, ㅣ , ^, ~, >>, <<     |
| 삼항 연산자 | (조건식)?(표현식1):(표현식2) |

# 산술 연산자(arithmetic operator)

| 연산자 종류 |     연산자     |
| :---------: | :------------: |
| 산술 연산자 | +, -, \*, /, % |

- `+`는 정수나 실수 뿐만아니라 문자열끼리도 가능하다.
  - `"Hello" + "World" = "HelloWorld"`
- 나머지 연산자인 `%`는 데이터 처리, 알고리즘 등 다양한 용도로 사용된다.

  - %의 활용 예시

    - `홀짝 검사`

    ```java
    int number = 5;
    if (number % 2 == 0) {
        System.out.println("짝수입니다."); // 나머지 0
    } else {
        System.out.println("홀수입니다."); // 나머지 1
    }
    ```

    - `순환 처리`

    ```java
    for (int i = 1; i <= 10; i++) {
        int number = i % 7;  // 1부터 7까지의 순환 처리
        System.out.println(number);
    }
    ```

    - `데이터 분할`
      - 100개의 데이터를 10개의 그룹으로 분할

    ```java
    int count1= 100;
    int count2 = 10;

    for (int i = 0; i < count1; i++) {
        int group = i % count2;  // 데이터를 그룹으로 분할
        // 분할된 그룹에 대한 처리
    }
    ```

## 연산자를 이용한 두 정수의 위치 바꾸기

- 두 정수의 위치를 바꾸기위해서는 보통 임시변수를 이용하여 두 변수를 전환한다.
- 연산자를 이용하면 임시변수를 선언하지 않고도 두 정수의 위치를 바꿀 수 있다.

```java
// nums[0] <-> nums[5]

//덧셈 뺄셈 연산자 이용
nums[0] = nums[0] + nums[5] -(nums[5] = nums[0]);
// 곱셈 나눗셈 연산자 이용
nums[0] = (nums[0] * nums[5]) / (nums[5] = nums[0]);

// 확인
System.out.printf("nums[0] : %d, nums[5] : %d", nums[0], nums[5]);
```

# 대입 연산자(assignment operator)

| 연산자 종류 | 연산자 |
| :---------: | :----: |
| 대입 연산자 |   =    |

# 증감 연산자(increment and decrement operators)

| 연산자 종류 | 연산자 |
| :---------: | :----: |
| 증감 연산자 | ++, -- |

- `++`
  - 1씩 증가하는 값을 반환
- `--`
  - 1씩 감소하는 값을 반환

## ++i(--i), i++(i--) 의 차이

- ++i(--i) : +1증가(1감소) 후 증가된 값 반환
- i++(i--) : 현재값 반환 후 +1증가(1감소)

```java
// ++i, i++
int x = 1;
x++;
System.out.println(x); // 2
System.out.println(++x); // 3
System.out.println(x++); // 3
System.out.println(x); // 4

// --i, i--
int x = 1;
x--;
System.out.println(x); // 0
System.out.println(--x); // -1
System.out.println(x--); // -1
System.out.println(x); // -2
```

# 비교 연산자(comparison operator)

| 연산자 종류 |         연산자         |
| :---------: | :--------------------: |
| 비교 연산자 | <<, , <, >>, >, ==, != |

- 자료형이나 객체의 동등성을 비교할 때는 `==`를 이용한다. 다만, String의 경우, 함수인 `equals()`를 이용하는 것에 주의하자.
  - `equals()`는 객체의 주소가 아닌 내용값 비교.`==`는 객체의 주소를 비교하므로 원하는 결과를 얻기 어렵다.

```java
String str1 = "Hello";
String str2 = "Hello";
String str3 = "World";

boolean isEqual1 = str1.equals(str2); // true
boolean isEqual2 = str1.equals(str3); // false

```

# 논리 연산자(logical operator)

| 연산자 종류 |   연산자    |
| :---------: | :---------: |
| 논리 연산자 | &&, ㅣㅣ, ! |

- 우선순위 : NOT > AND > OR
- 가독성을 높이기 위해 괄호를 사용하기도 한다.

```java
int x = 5;
int y = 10;
int z = 15;

if ((x > y && y < z) || (x < y && y > z)) {
    System.out.println("true.");
}

```

# 비트 연산자(bitwise operator)

| 연산자 종류 |        연산자        |
| :---------: | :------------------: |
| 비트 연산자 | &, ㅣ , ^, ~, >>, << |

- &(교집합 연산자) : 거짓+참 = 거짓, 0011 & 0111 -> 0011
- |(합집합 연산자) : 거짓 + 참 = 참, 0011 | 0111 -> 0111
- ^(차집합 연산자) : 0011 ^ 0111 -> 0100
- ~(비트 부정 연산자) : ~0011 -> 1100

## 쉬프트 연산자

- 이진수로 표현된 값을 비트 단위로 이동시켜주는 연산자.
- <<(왼쪽으로 이동)
  - 0011 << 2 => 1100
- > > (오른쪽으로 이동)
  - 0011 >> 2 => 0000
  - 예외) 1010 >> 2 => 1110
    - 왜? => `부호비트 때문에` 양수를 나타내기 위해 맨 앞자리를 따라 1을 작성한다.

## 쉬프트 연산자의 응용 - 비트맵 파일의 해상도 출력하기

- 쉬프트 연산자를 이용하면, 비트맵 파일의 크기나 해상도를 출력할 수 있다.
- FileInputStream과 read();, <<을 이용하여 출력한다.

```java
public static void main(String[] args) throws IOException {
		int n1, n2, n3, n4, n5, n6, n7, n8;
		int size, weight;

		FileInputStream fis = new FileInputStream("res/pic1.bmp");

		for (int i = 0; i < 18; i++) {
			fis.read();
		}

		n1 = fis.read();
		n2 = fis.read();
		n3 = fis.read();
		n4 = fis.read();
		n5 = fis.read();
		n6 = fis.read();
		n7 = fis.read();
		n8 = fis.read();

        // <<을 이용하여 바이트의 역순으로 출력하기.

		size = n1 | n2 << 8 | n3 << 16 | n4 << 24;

		System.out.println(size);

		weight = n5 | n6 << 8 | n7 << 16 | n8 << 24;

		System.out.println(weight);

		System.out.printf("%d x %d", size, weight);

		fis.close();

	}
```

### 쉬프트 연산자와 &&, 16진수를 이용하여 byte단위 초기화하기

- 비트맵 파일의 해상도를 출력하기위해 바이트 단위로 읽은 값을 나열하는 과정에서 배운 내용이다.
- int n1~ n4를 이용하여 width값을 구해본다.

```java
FileInputStream fis = new FileInputStream("파일명.bmp")

// read()를 이용한 byte 읽기
n1 = fis.read();
n2 = fis.read();
n3 = fis.read();
n4 = fis.read();

// '<<'과 16진수와의 교집합을 이용한 바이트 초기화
width = (n1 << 0) & 0x000000ff |
    (n2 << 8) & 0x0000ff00 |
    (n3 << 16)  & 0x00ff0000 |
    (n4 << 24)  & 0xff000000 ;

System.out.printf("width : %d\n",width);
```

- 해당 코드를 보면 byte를 나열할 때, 비트 쉬프트 연산자인 '<<'를 이용하는 걸 볼 수 있는데, 이는 byte가 bit로 구성되어 있어, n1을 제일 우측으로 해서 n4를 제일 좌측에 위치하도록 배열하는데 사용할 수 있다.
  - 왜 8 배수로 이동?
    - 1byte = 8bit로 1byte로 읽은 n1~n4를 각각 이동시키기위해선 bit를 8칸씩 이동시켜야한다.
  - 그럼 '<<'만 써서 이동시키면 되지 않나? 왜 0x000000ff의 16진수와 교집합을 통해서 식을 구성할까?
    - 1byte의 8bit중 `첫번째 bit는 부호비트`이다. 부호비트는 값이 양수인지 음수인지를 나타내는 값으로, 만약 byte가 이동할때 부호비트가 바뀐다면 음수로 변형되어 값이 완전히 바뀔 수 있는 위험성이 존재한다. 따라서 byte를 초기화할 필요가 있다.

# 삼항 연산자(ternary operator)

| 연산자 종류 |            연산자            |
| :---------: | :--------------------------: |
| 삼항 연산자 | (조건식)?(표현식1):(표현식2) |

- 조건식이 참일 경우 표현식1이 반환되고 거짓일 경우 표현식 2가 반환된다.

## 삼항 연산자와 if-else문의 차이점

1. 삼항 연산자는 `값의 할당에 사용`되며, 표현식의 결과를 반환한다. 반면, if-else문은 `코드 블록을 실행하고 제어 구조를 변경`한다.
2. 삼항 연산자는 `간단한 조건`에만 사용 가능하며, `복잡한 조건`을 처리할 때에는 if-else가 더 적합하다.
3. 삼항 연산자는 `코드를 간결하게 표현`하지만, `가독성`은 if-else문이 더 뛰어난 편이다.

### 성적 평가에서의 삼항 연산자와 if-else문의 차이

- 삼항 연산자

```java
int score = 80;
String grade = (score >= 90) ? "A" : (score >= 80) ? "B" : (score >= 70) ? "C" : "F";
System.out.println("Grade: " + grade);

```

- if-else문

```java
int score = 80;
String grade;
if (score >= 90) {
    grade = "A";
} else if (score >= 80) {
    grade = "B";
} else if (score >= 70) {
    grade = "C";
} else {
    grade = "F";
}
System.out.println("Grade: " + grade);
```

- 삼항 연산자를 사용하면 간결하게 한줄로 표현되지만, 가독성은 if-else문이 더 뛰어나다는걸 알 수 있다. 따라서 복잡한 조건식이 포함될 경우, if-else문을 사용하는걸 권장한다.
