- [Optional 클래스](#optional-클래스)
  - [Optional을 사용하는 이유?](#optional을-사용하는-이유)


# Optional 클래스

> 자바의 정석과 김영한님의 스프링 입문 강의에서 배운 내용을 토대로 작성
> 

## Optional을 사용하는 이유?

- Optional 클래스는 모든 타입을 사용할 수 있다.
- 안정적으로 null값을 다루기 위함 → 간접적인 null 관리
    - 직접 null값을 관리하면, NullPointerException이 발생할 확률이 높아진다.
- 일반적으로 null값을 체크하기위해 조건문을 활용하는데 이는 코드 가독성을 떨어뜨린다.