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
- 주의할점!
  - 파일을 복사할때, NextLine()의 줄바꿈으로 인해 1줄이 더 추가된 상태로 복사가 된다. 이는 복사된 파일과 복사 대상 파일의 용량이 서로 다르게 저장되는 결과를 낳는다.
  - NextLine은 해당 라인을 읽은 후에, `자동으로 커서가 줄바꿈이 된다.`

```java
FileInputStream fis = new FileInputStream("읽어들일 파일명.data")
Scanner fscan = new Scanner(fis);
FileOutputStream fos = new FileOutputStream("생성할파일명.data")

// hasNextLine을 이용하여 모든 문자열 입력받기
while(fscan.hasNextLine())
	{
		String line = fscan.nextLine();
		fout.print(line);

		if(fscan.hasNextLine()) // if를 이용한 마지막 줄바꿈 방지
			fout.println();
	}

```

- `while(fscan.hasNextLine())`을 이용하여 모든 문자열을 입력받고 입력값이 없을때 false를 반환하도록 하여 반복을 중지한다.
- `if(fscan.hseNextLine())`을 이용하여 마지막 문자열을 읽은 후, nextLine()의 자동줄바꿈 이후에는 읽어들일 문자열이 없게 되므로 false를 반환하며, if문 내의 명령은 제외된다. 따라서 줄바꿈을 수행하지 않고 생성할파일명의 마지막 줄바꿈을 방지할 수 있다.

## hasNextLine()

- 입력되는 `문자열의 유무에 따라 true or false`의 boolean형의 값을 반환하는 Scanner 클래스 내의 메소드이다.
- 주의할 점은 hasNextLine()은 입력값을 반환하지 않으며 반한될 `문자열의 존재 유무만 확인이 가능`하다.
- 더이상의 문자열이 존재하지 않을때까지 반복문을 수행할 때, 주로 사용된다.

```java
public class Main {
    public static void main(String[] args) {
        Scanner scan = new Scanner(System.in);

        System.out.println("문자열을 입력하세요: ");
        String input = scan.nextLine();

        if (scan.hasNextLine()) {
            System.out.println("다음 줄에 입력된 내용이 있습니다.");
        } else {
            System.out.println("다음 줄에 입력된 내용이 없습니다.");
        }

        scan.close();
    }
}
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
