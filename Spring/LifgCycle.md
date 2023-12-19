- [빈 생명주기 콜백](#빈-생명주기-콜백)
  - [인터페이스(InitializingBean, DisposableBean)](#인터페이스initializingbean-disposablebean)
  - [**빈 등록 초기화**, **소멸 메서드 지정**](#빈-등록-초기화-소멸-메서드-지정)
  - [**애노테이션** @PostConstruct, @PreDestroy](#애노테이션-postconstruct-predestroy)

# 빈 생명주기 콜백

- 필요한 이유
    - 데이터베이스 커넥션 풀이나, 네트워크 소켓처럼 애플리케이션 시작 시점에  필요한 연결을 미리 해두고, 애플리케이션 종료 시점에 연결을 모두 종료하려면, 객체의 초기화 및 종료 작업이 필요하다.
- 스프링 빈의 이벤트 라이프 사이클
    - **스프링 컨테이너 생성 → 스프링 빈 생성 → 의존관계 주입 → 초기화 콜백 → 사용 → 소멸전 콜백 → 스프링 종료**
- **객체 생성과 초기화를 분리하자**
    - 최대한 스프링에게는 객체 생성 시 거기에만 집중하도록 만든다.
    - 생성자는 필수 정보(파라미터)를 받고, 메모리를 할당해서 객체를 생성하는 책임을 갖는다.
    - 반면에 초기화는 생성된 값을 사용해서 외부 커넥션을 연결하는 등 무거운 동작을 수행.
- 스프링은 크게  3가지 방법으로 빈 생명주기 콜백을 지원한다.
    - `인터페이스(InitializingBean, DisposableBean)`
    - `설정 정보에 초기화 메서드, 종료 메서드 지정`
    - `@PostConstruct, @PreDestroy 애노테이션 지원`
- 테스트 코드 -`NetworkClient`, `BeanLifeCycleTest`
    - `NetworkClient`
        
        ```java
        public class NetworkClient {
            private String url;
        
            public NetworkClient() {
                System.out.println("생성자 호출, url = " + url);
        
            }
        
            public void setUrl(String url) {
                this.url = url;
            }
        
            //서비스 시작시 호출
            public void connect() {
                System.out.println("connect: " + url);
            }
        
            public void call(String message) {
                System.out.println("call: " + url + " message = " + message);
            }
        
            //서비스 종료시 호출
            public void disconnect() {
                System.out.println("close: " + url);
            }
        
            @PostConstruct
            public void init() {
                System.out.println("NetworkClient.init");
                connect();
                call("초기화 연결 메시지");
            }
        
            @PreDestroy
            public void close() {
                System.out.println("NetworkClient.close");
                disconnect();
            }
        
        }
        ```
        
    - `BeanLifeCycleTest`
        
        ```java
        public class BeanLifeCycleTest {
        
            @Test
            public void lifeCycleTest() {
                ConfigurableApplicationContext ac = new AnnotationConfigApplicationContext(LifeCycleConfig.class);
                NetworkClient bean = ac.getBean(NetworkClient.class);
                ac.close(); // 스프링 컨테이너 종료 -> ConfigurableApplicationContext 필요
            }
        
            @Configuration
            static class LifeCycleConfig {
                @Bean
                public NetworkClient networkClient() {
                    NetworkClient networkClient = new NetworkClient();
                    networkClient.setUrl("http://hello.com");
                    return networkClient;
                }
            }
        }
        ```
        

## 인터페이스(InitializingBean, DisposableBean)

- 굉장히 초창기에 나온 방법, 거의 사용하지 않는 방법.
- `InitializingBean이란?`
    - **스프링 빈이 만들어지고 초기화 될 때** 호출되는 인터페이스
- `DisposableBean이란?`
    - **스프링 빈이 소멸되기 직전에** 호출되는 인터페이스
- **사용방법**
    
    ```java
    public class NetworkClient implements InitializingBean, DisposableBean {
        private String url;
    
    // 의존관계 주입 이후 호출
        @Override
        public void afterPropertiesSet() throws Exception {
            System.out.println("NetworkClient.afterPropertiesSet");
            connect();
            call("초기화 연결 메세지");
        }
    
        @Override
        public void destroy() throws Exception {
            System.out.println("NetworkClient.destroy");
            System.out.println("close: " + url);
        }
    }
    ```
    
    - 생성할 빈에 인터페이스를 상속시켜 `afterPropertiesSet()`, `destroy()` 을 오버라이드하여 메소드 호출
- 결과
    
    ```java
    생성자 호출, url = null
    NetworkClient.afterPropertiesSet
    connect: http://hello.com
    call: http://hello.com message = 초기화 연결 메세지
    NetworkClient.destroy
    close: http://hello.com
    ```
    
- 초기화 인터페이스의 단점
    - 스프링 전용 인터페이스이다. **스프링 인터페이스에 의존**한다.
    - 초기화, 소멸 **메서드 이름 변경 불가**
    - 코드를 고칠 수 없는 **외부 라이브러리에 적용할 수 없다.**

## **빈 등록 초기화**, **소멸 메서드 지정**

- `init, close 메서드`를 이용하여 초기화, 소멸 콜을 호출한다.
- `NetworkClient`
    
    ```java
    public class NetworkClient {
    
    ...
    
    		public void init(){
            System.out.println("NetworkClient.init");
            connect();
            call("초기화 연결 메시지");
        }
    
        public void close(){
            System.out.println("NetworkClient.close");
            disconnect();
        }
    }
    ```
    
- 테스트 코드
    
    ```java
    @Configuration
    static class LifeCycleConfig {
    
        **@Bean(initMethod = "init", destroyMethod = "close")**
        public NetworkClient networkClient() {
            NetworkClient networkClient = new NetworkClient();
            networkClient.setUrl("http://hello.com");
            return networkClient;
        }
    
    }
    ```
    
    - @Bean의 `initMethod`, `destroyMethod`를 이용하여 호출한다.
- 특징
    - 메서드 이름을 **임의로 지정 가능**
    - 스프링 빈이 **스프링 코드에 의존하지 않는다.**
    - 설정 정보를 이용하기 떄문에 코드를 변경할 수 없는 **외부 라이브러리에도 초기화, 종료 메서드를 사용할 수 있다.**
- 종료 메서드 추론 (destroyMethod)
    - 라이브러리는 대부분 close, shutdown이라는 이름으로 종료 메서드를 선언한다.
    - destoryMethod는 기본값이 (inferred)(추론)으로 되어있고 해당 추론 기능은 close, shutdown이름으로 종료 메서드를 자동 호출한다.
    - 따라서 직접 스프링 빈으로 등록하면 종료 메서드는 따로 적어주지 않아도 된다.
    - 추론 기능을 사용하기 싫으면 `destroyMethod=""` 처럼 빈 공백으로 지정해주면 된다.

## **애노테이션** @PostConstruct, @PreDestroy

- 최신 스프링에서 적극 권장하는 방법
- 특징
    - 컴포넌트 스캔이랑 잘 어울림.
    - 패키지가 java의 표준에 속한다. → 스프링 말고도 다른 컨테이너에서도 동작 가능
        - `import jakarta.annotation.PostConstruct;`
        - `import jakarta.annotation.PreDestroy;`
    - 코드를 고칠 수 없는 외부 라이브러리에는 적용하지 못한다. → `@Bean` 의 기능을 이용하자.
- 사용방법
    - `NetworkClient`
        
        ```java
        public class NetworkClient {
        		@PostConstruct
        		public void init() {
        		    System.out.println("NetworkClient.init");
        		    connect();
        		    call("초기화 연결 메시지");
        		}
        		
        		@PreDestroy
        		public void close() {
        		    System.out.println("NetworkClient.close");
        		    disconnect();
        		}
        }
        ```
        
        - 초기화와 종료 메서드에 각각 @PostConstruct, @PreDestroy 을 달아주면 된다.
    - 테스트 코드
        
        ```java
        @Configuration
        static class LifeCycleConfig {
            @Bean
            public NetworkClient networkClient() {
                NetworkClient networkClient = new NetworkClient();
                networkClient.setUrl("http://hello.com");
                return networkClient;
            }
        }
        
        ```