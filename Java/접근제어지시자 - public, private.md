# 접근제어지시자의 필요성

> 만약 A 캡슐에서 B 캡슐의 데이터를 이용하고자 한다면 어떻게 해야 할까?

B캡슐은 데이터를 A에게 허락할지 말지 권한을 갖고 있다. 하지만 데이터를 마구잡이로 허락했다가는 B캡슐의 밖에서 오류가 발생하는 `캡슐 꺠짐 현상`이 발생할 수 있다. 또한 `보안`에도 취약해질 수 있다.

이 때 필요한 개념이 바로 접근제어지시자이다.

접근 제어 지시자에는 `public, protected,private`가 있다.

# Public

- 다른 클래스에서도 엑세스가 가능하다.
- 캡슐화를 의식할 때, 주의해야한다.

## public의 장점

- 접근 용이성

  - 모든 클래스에서 접근이 가능하므로 쉽게 사용 및 수정이 가능하다.

- 코드 재사용
  - 다른 클래스에서 사용이 가능하기에 코딩하는 시간과 노력을 절약할 수 있다.

## public의 단점

- 보안 위험
- 취약한 관리체계
  - 다른 클래스에서 사용 및 수정하는 권한을 통제하기 어렵다.

# Private

- 선언된 클래스 내에서만 엑세스가 가능하다.

## private의 장점

- 강력한 보안
  - 선언된 클래스 내에서만 수정이 가능하므로 보다 안전하다.
- 제어
  - 선언된 클래스 내의 메서드로만 수정할 수 있으므로 사용 및 수정을 통제하기 수월하다.

## private의 단점

- 힘든 코드 재사용
  - 다른 클래스에서 재사용이 어려우므로 코딩에 추가적인 노력이 필요하다.

# public, private 예시

## 고객정보

```java
public class Person {
   public String name;
   private int creditCard_number;
}
```

이름, 성별 등은 공개되어도 되지만 카드번호나 CVC번호 등은 공개되면 안되는 정보이다.

따라서, `이름, 나이, 성별 등의 공개 정보는 public`으로 선언하고 `신용카드 번호, CVC 번호 등의 비공개 정보는 private`로 설정하면 된다.

# Protected

- 외부에서 공유되지 않고 오로지 `같은 패키지나 자식 클래스`에게만 공유되어야 할 때 사용.
- 같은 패키지가 아닌 자식 클래스에게만 공유되고자 한다면 주의해서 작성할 필요가 있다.

```java
// ExamConsole (추상클래스)
public abstract class ExamConsole {

  protected abstract void printEx(); //자식에게만 공유

}

// NewExamConsole (자식클래스)
public class NewExamConsole extends ExamConsole {

  @override
  public void printEx() {
    // printEx() 구현 내용
  }
}


```
