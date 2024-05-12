## Garbage Collection이란?

가비지 컬렉션(Garbage Collection, GC)란, 자바의 메모리 관리 방법 중 하나로 동적 메모리 영역에서 쓰이지 않는 객체를 제거하여 메모리 효율을 증가시키는 프로세스이다.

<img width="524" alt="1" src="https://github.com/devhoody/TIL/assets/124743189/d8a64e18-3b27-4f60-88dd-b3ceca4af8bf">


## GC 장단점

장점

- 메모리 누수를 막음
- 참조되지 않은 객체에 접근을 막음

단점

- GC 작업은 애플리케이션이 중지된 후 실행된다. → Stop the World 발생
- 개발자는 GC 작업이 언제 수행되는지 모른다.

# GC 알고리즘

> GC는 어떻게 참조되지 않은 객체를 구별할 수 있을까?
> 

GC는 기본적으로 Reachable 과 Unreachable을 구분하여 객체 참조 여부를 확인한다.

- Reachable : 객체가 참조되어 있는 상태
- Unreachable : 객체가 참조 되어 있지 않은 상태 (GC의 대상)
    
<img width="841" alt="2" src="https://github.com/devhoody/TIL/assets/124743189/c60006b0-088b-42a9-8667-11a390035ea9">
    

## Reference Counting

<img width="721" alt="3" src="https://github.com/devhoody/TIL/assets/124743189/2abc06ef-9d5a-4a2f-afe6-e262e993ad05">

힙 영역에 있는 인스턴스가 Root Space 영역으로부터 참조되고 있는 개수를 카운팅해서 0이면 제거한다.

자기 자신을 참조하는 순환 참조시 1이 유지된 상태이기 때문에 제거되지 못한다는 단점이 있다. 위의 그림에서 오른쪽을 보면 루트로부터 참조되어 있지 않은 객체가 순환 참조되어 카운팅이 1이 되어있다. GC는 해당 객체가 참조되어 있는 상태로 판단하고 제거하지 않아 메모리 낭비가 발생하게 된다.

이를 해결하는 것이 Mark and Sweep방식이다.

> Root Space : 객체를 참조하고 있는 참조 변수들의 공간, 종류로는 스택 영역의 로컬 변수, 메서드 영역의 static 변수 등이 있다.
> 
> 
<img width="814" alt="4" src="https://github.com/devhoody/TIL/assets/124743189/788a7444-bbef-4537-9ab6-8be3cdb32a34">
> 

## Mark and Sweep

<img width="651" alt="5" src="https://github.com/devhoody/TIL/assets/124743189/6a4c2b30-f0c4-4c3a-8d17-75b01327283e">


루트 스페이스에서 참조하는 경로를 Mark하여 참조되어 있는지를 판단한다. 루트로부터 선을 이어 연결되어 있는 것끼리 마킹해 놓는다고 생각하면 이해될 것이다.

마킹이 되어 있지 않은 객체들은 제거된다. 이를 Sweep이라고 부른다. 이후 Compaction 이라는 작업을 수행하여 제거된 후 남아있는 메모리 파편들을 한 곳에 모아둔다.

현재 자바에서 사용되고 있는 GC 알고리즘이다.

## GC 특징

위에서도 말했다시피 GC의 단점이자 특징으로 두가지가 존재한다. 이번에는 이 단점을 Java에서는 어떻게 해결하는지 살펴보자.

- **GC를 의도적으로 실행해야한다.**
- **애플리케이션과 GC를 병행해야한다.**

# 힙 영역에서의 GC 동작 방식

GC를 의도적으로 실행하기 위해서는 힙 영역에서의 동작 방식을 보면 된다.

힙 영역은 **Young Generation(Eden, Survival 0, Survival 1), Old Genetation** 으로 나뉘어 있다. 

<img width="851" alt="6" src="https://github.com/devhoody/TIL/assets/124743189/4ea196be-5e1b-4d37-9907-7ff86ff0862e">
## Young Generation(YG)

짧게 살아있는 객체들이 머무는 공간

minorGC가 발생한다.

### Eden

new를 통해 새롭게 생성된 객체가 저장되는 영역

### Survival 1, Survival 2

Eden 영역에서 살아남은 객체들이 넘어오는 영역

이때 agebit는 +1이 되고, Survival1 과 2 영역은 둘 중 하나가 비어있는 상태로 유지된다.

## Old generation

YG에서 살아남은 객체들이 머무는 공간

minorGC가 발생한다.

## 힙 영역에서의 GC 과정

1. 새롭게 생성된 인스턴스는 Eden에 저장되고 Eden 영역이 꽉 차게 되면 Survival 0 영역으로 사용하는 객체만 이동되고 나머지는 제거되는 minor GC가 일어난다. - `age bit +1` 
2. 또다시 Eden영역에 새로운 인스턴스가 채워지게 되고 꽉차게되면 이번에는 Survival 1 영역으로 Survaval0에 있는 인스턴스와 함께 minor GC가 일어난다. 이때, Survival 0 영역은 비어있다. - `age bit +1`
3. 또다시 Eden 영역이 채워지고 ,, 이 minor GC가 반복되면 agebit가 오르게 되고, 일정 수준이 되면 oldGeneration 영역으로 이동하게 된다. 이를 Promotion이라고 한다.

> agebit : minor GC가 일어날 때마다 늘어나는 수명이라고 생각하면 된다. 자바 8의 경우 15이면 OG로 Promotion된다.
> 

## 애플리케이션과 GC의 병행

JVM은 GC를 수행할 때, 애플리케이션을 잠시 중지한다. 이를 **Stop the World**라고 한다.

최적화가 잘되어있는 경우, Stop the World의 시간이 최소화되어 있는 경우이다.

GC의 종류에 따라 Stop the World의 시간이 다르다.

### Stop the World

<aside>
💡 Stop the World

- GC를 수행하기 위해 애플리케이션이 잠시 중단되는 현상
    
<img width="316" alt="7" src="https://github.com/devhoody/TIL/assets/124743189/64271f0a-92dd-4b14-9d7f-cd4582fee193">
    
</aside>

# GC 종류

## Serial GC

<img width="289" alt="8" src="https://github.com/devhoody/TIL/assets/124743189/b90e6371-0023-4434-ae5f-cc8b08626b31">


- 스레드 1개로 Stop the World의 시간이 비교적 길다.

## Parallel GC

<img width="376" alt="9" src="https://github.com/devhoody/TIL/assets/124743189/90e2f955-dffa-426e-b794-59fbb7a4ef4e">


- 멀티 스레드로 Stop the World의 시간이 Serial GC보다는 짧음

## CMS GC

<img width="435" alt="99" src="https://github.com/devhoody/TIL/assets/124743189/73332e7c-fb59-4463-b7cf-893ab96ad851">


- 애플리케이션과 GC를 병행한다.
- 하지만 Compaction 기능을 제공하지 않음 → G1 GC로 대체
- `자바 8`에서 기본 적용

> compaction : GC가 sweep 이후 메모리 파편화를 축소하는 기능
> 

## G1 GC

<img width="515" alt="999" src="https://github.com/devhoody/TIL/assets/124743189/dd58646a-a422-472c-a4fb-1f80831eb8ba">


- 가비지 퍼스트 GC
- Heap을 일정 크기로 나누어 G1 GC가 스스로 YG, OG 영역의 비중을 결정한다.
- `Java 9이상`에서 기본 적용

# Reference

https://rebelsky.cs.grinnell.edu/Courses/CS302/99S/Presentations/GC/

[https://inpa.tistory.com/entry/JAVA-☕-가비지-컬렉션GC-동작-원리-알고리즘-💯-총정리](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EA%B0%80%EB%B9%84%EC%A7%80-%EC%BB%AC%EB%A0%89%EC%85%98GC-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%F0%9F%92%AF-%EC%B4%9D%EC%A0%95%EB%A6%AC)

https://blog.heroku.com/incremental-gc

https://youtu.be/FMUpVA0Vvjw?si=3UoOtLFrZWdmvf0q