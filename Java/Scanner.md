# Scanner

- 기본적으로 `문자열`을 받을 수 있으며 `java.util`에 속하는 클래스
- 간편하게 다양한 유형(파일, 키보드 등)의 입력을 처리할 수 있다.
- Scanner를 사용한 후에는 반드시 `close()`를 습관화하자.
  - close()를 통해 `메모리 누수`를 막을 수 있다.

## Next()

- `단어 단위의 문자열`을 입력받는다.
- 입력된 문자열에서 `스페이스나 탭, 개행`을 기준으로 나누어 문자열을 읽는다.

```java
Scanner scan = new Scanner(System.in);

System.out.print("이름을 입력하세요: ");
String name = scan.next();

System.out.printf("입력된 이름: %s", name);
scan.close();
```

## NextInt()

- `단어 단위의 정수값`을 입력받는다.
- 해당 정수 뒤에 있는 공백 문자를 남겨둡니다.

```java
Scanner scan = new Scanner(System.in);

System.out.print("나이를 입력하세요: ");
int age = scan.nextInt();

System.out.printf("입력된 나이: %d", age);
```

## NextLine()

- `한 줄의 문자열`을 입력받는다.
- 개행 문자(줄바꿈) 이전까지의 모든 문자열을 읽는다.

```java
Scanner scanner = new Scanner(System.in);

System.out.print("소개해주세요: ");
String intro = scanner.nextLine();

System.out.printf("입력된 소개: %s", intro);
```

## FileInputStream, Scanner의 파일 입력

- 만약, 파일에 있는 성적을 kor1, kor2, kor3에 입력받기 위한다면 다음과 같이 코드를 작성한다.

```java
FileInputStream fis = new FileInputStream("res/exam.txt");
		Scanner fscan = new Scanner(fis);

		kor1 = fscan.nextInt();
		kor2 = fscan.nextInt();
		kor3 = fscan.nextInt();

        fscan.close();
        fis.close();
```

- FileInputStream을 이용하여 가져올 파일을 지정한 후, Scanner(fis)을 이용하여 Scanner가 받을 값이 있는 파일을 지정한다.
- nextInt()를 이용하여 exam.txt에 있는 값을 한 단어씩 정수값을 입력받는다.
