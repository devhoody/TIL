# Getter와 Setter

Getter와 Setter는 객체지향프로그래밍의 `캡슐화를` 진행하는데 중요한 역할을 한다.
Getter와 Setter는 각각 클래스의 값을 검색하고 수정하는데 사용되는 메서드이다.

## Getter와 Setter를 사용하는 이유

### 캡슐화 및 데이터 은닉

Getter와 Setter는 클래스 내부를 캡슐화하고 직접 접근하지 못하도록 할 수 있다. 이는 `클래스 내부의 일관성을 유지`하는데 도움이 된다.

### 유연성 및 코드 유지 관리

Getter와 Setter는 클래스를 사용하는 `외부 코드에 영향을 주지 않고 클래스 내부의 구현을 변경할 수 있다.` 이렇게 하면 코드 유지 관리가 수월하고 향후 변경을 쉽게 할 수 있다.

## Getter와 Setter 구현

```java
// getter
public <datatype> get(){
    // return
}

// setter
public <datatype> set(){
    // return
}
```

### 명명규칙

- getter - 일반적으로 `get`이라는 단어로 시작하고 뒤에 속성 이름이 온다. `getKor()`
- setter - 일반적으로 'set'이라는 단어로 시작하고 뒤에 속성 이름과 내용이 온다.
  `setKor(kor)`

### 속성에 접근하기

예를 들어, 정수형인 kor값을 선언하고 이에 대한 getter와 setter에 대한 내용은 다음과 같다.

```java
private int kor;

public int getKor() {
		return kor;
	}
	public void setKor(int kor) {
		this.kor = kor;
	}
```

위의 예에서 getter 메소드 `getkor()은 kor값을 반환`하고 setter 메소드 `setKor()은 kor값에 대한 새 값을 세팅`한다.

## 추가적인 예시

> 이해를 위해 추가적으로 예시를 만들어보았다.

### 고객 정보

- 고객의 이름은 공개하되, 나이는 비공개로 설정한다.

```Java
public class Customer {
    // name, age 선언
    public String name;
    private int age;

    // Getter - getName()
    public String getNmae() {
        return name;
    }

    // Setter - setName()
    public void setName(String name) {
        this.name = name;
    }

    // Getter - getAge()
    public int getAge() {
        return age;
    }

    // Setter - setAge()
    public void setAge() {
        this.age = age;
    }
}
```

### 은행 계좌잔액

- 은행 계좌번호는 공개, 해당 계좌의 잔고는 비공개 처리한다.

```Java
public class BankAccount {
    public int accountNumber;
    private double balance;

   public int getAccountNumber() {
      return accountNumber;
   }

   public void setAccountNumber(int accountNumber) {
      this.accountNumber = accountNumber;
   }

   public double getBalance() {
      return balance;
   }

   public void setBalance(double balance) {
      this.balance = balance;
   }
}
```

- this는 생략이 가능하다.

## \* Reference

- 뉴렉처 유튜브

  [자바 객체지향 강의 8강 - Getters와 Setters 그리고 이것을 써야하는 이유](https://youtu.be/cStPUeKgSCU)
