- [테스트케이스 만들기](#테스트케이스-만들기)
  - [테스트가 필요한 이유?](#테스트가-필요한-이유)
  - [테스트케이스를 만드는 방법](#테스트케이스를-만드는-방법)
  - [**Assertions 클래스**](#assertions-클래스)


# 테스트케이스 만들기

- 클래스나 메소드를 구현 후, 테스트를 하는 것은 거의 필수가 되고있다. 그래서 조금씩 꾸준히 연습하고자한다.

## 테스트가 필요한 이유?

- 혼자서 프로젝트를 할 수도 있지만, 실무에서 협업은 필수적이다. 나와 다른 사람의 코드를 서로 교류할 때, 구현이 되는지, 여러 메소드가 각자 따로 실행이 제대로 되는지 확인하는 작업이 꼭 필요하다.

## 테스트케이스를 만드는 방법

- 만들고자하는 클래스나 메소드의 경로에 맞춰 똑같이 test클래스를 만들어준다. 이 때, 관례상 테스트하고자하는 클래스명 끝에 Test륿 붙여준다.
    - ex) `MemberRepository` ⇒ `MemberRepositoryTest`
- `@Test` 를 이용해서 테스트하고자하는 메소드를 구현한다.
    - member를 저장하는 save 메소드
        
        ```java
        @Test
        public void save(){
            // given
            Member member = new Member();
            member.setName("spring");
        
            // when
            Member result = repository.save(member);
        
            // then
            assertThat(result).isEqualTo(member);
            Assertions.assertThat(result).isEqualTo(member);
        }
        ```
        
    - *김영한님의 TIP*
        - 테스트 메소드를 만들때, **삼단계(given, when, then)**를 떠올리면 쉽게 작성할 수 있다.
- test 클래스를 실행하여 그 안에 구현된 test메소드들을 동시에 실행이 가능하다.
    - 하지만 여기서 주의할 점은 기존에 구현한 클래스처럼 위에서부터 순서대로 메소드가 실행되는게 아닌, test메소드들이 순서가 없이 실행되기 때문에 test의 정확도가 낮아진다.
        - 이 점을 해결하기 위해 각 test가 끝날때마다 이용한 값들을 초기화시켜야한다.
        - `@AfterEach`
            - 스프링에서는 @AfterEach를 이용하여 각 메소드가 끝날때마다 해당 메소드를 실행하여 값을 초기화 시킬 수 있다.
                
                ```java
                @AfterEach
                public void afterEach(){
                    repository.clearStore();
                }
                ```
                

## **Assertions 클래스**

- Assertions 클래스란? - 우선 김영한님의 스프링 입문강의에서 배운대로 적어본다.
    - 메소드를 test할 때, 구현한 결과물과 내가 인위적으로 만든 값이 서로 일치하는지 확인하고자할 때, 사용하는 클래스.
- 어떻게 사용하나?
    - `Assertions.*assertThat*(**result**).isEqualTo(**member**);`
        - 내가만든 member 객체와 구현한 메소드를 통해 도출된 result가 일치하는지 확인하는 코드이다.
        - Assersions를 따로 import시켜 생략할 수 있다.
            - *`assertThat*(result).isEqualTo(member);`
    - ex)findAll 메소드의 경우, List를 반환하므로 size를 이용하여 확인할 수 있다.
        - *`assertThat*(**result.size()**).isEqualTo(**2**);`