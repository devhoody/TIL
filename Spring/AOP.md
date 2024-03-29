- [AOP](#aop)
  - [특징](#특징)
- [동작원리](#동작원리)


# AOP

- AOP란?
    - 스프링 컨테이너에 있는 어떤 객체를 직접 불러오기 전에 가짜(프록시) 객체를 만들어 부가적인 공통 관심 사항 관련 기능을 수행하도록 만드는 프로그래밍 기법
- AOP를 사용하는 이유
    - 만약, 모든 메소드(1000개)의 호출 시간을 측정하고자 한다면 1000개의 메소드에 직접 시간 호출 코드를 작성해야하는 번거로움이 존재.
    - AOP를 사용한다면, 하나의 클래스에서 시간 측정 로직을 구현하여 모든 메소드에 적용이 가능하다.
- 장점
    - 공통 관심 사항(시간 측정 등) 과 핵심 관심 사항(회원가입, 목록 조회 등)을 따로 나누어 시간을 측정할 수 있다.
        - 핵심 관심 사항을 건드릴 필요가 없다.

## 특징

- @Component 도 좋지만, 일반적인 경우, 빈에 직접 꽂아주는 것이 좋다.

<aside>
💡 TimeTraceApp을 직접 빈에 등록할 경우 순환 참조 오류 발생

- 에러 발생
    
    <img width="1291" alt="1" src="https://github.com/devhoody/TIL/assets/124743189/c207b4ce-a4fc-4778-a685-99112d37c90e">

    
- 원인
    - AOP를 지정하는 @Around는 SpringConfig에 있는 빈도 AOP로 지정한다. TimeTraceAop 자기 자신을 생성하는 것이 되므로 순환 참조가 발생한다.
- 해결방법
    - TimeTraceAop에서 SpringConfig를 AOP 대상에서 제외시키면 해결된다.
        - @Around에 !target을 이용하여 추가
        
        ```java
        @Around("execution(* hello.hellospring..*(..)) &&  !target(hello.hellospring.SpringConfig)")
        
        ```
        
        <img width="700" alt="2" src="https://github.com/devhoody/TIL/assets/124743189/578f6263-e2d4-4363-a9c3-9fbe5b9ea87f">

        
</aside>

# 동작원리

- AOP 적용 전 의존관계
    - controller → service
- AOP 적용 후 의존관계
    - `controller → 프록시 service → 진짜 service`