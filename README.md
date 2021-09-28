# 김영한 스프링강의

### 스프링의 핵심

좋은 객체 지향적으로 개발할수 있도록 도와주는 프레임워크

### 다형성

역할과 구현을 분리

유연하고 변경이 용이함.

확장 가능한 설계

클라이언트에 영향을 주지 않는 변경 가능

### 다형성의 본질

클라이언트를 변경하지 않고 서버의 구현기능을 유연하게 변경 할 수 있다.

### SOLID

클린코드로 로버트 마틴이좋은 객체 지향 설계 5가지 원칙을 정리

SRP(Single Responsibility Principle) : 단일 책임 원칙

한 클래스는 하나의 책임만 가져야 한다.

중요한 기준은 변경이며 변경이 있을때 파급 효과가 적으면 단일 책임 원칙을 잘 따른것

OCP(Open/Closed Principle) : 개방-폐쇄 원칙

확장에는 열려 있으나 변경에는 닫혀 있어야 한다.

LSP(Liskov Substitution Principle) : 리스코프 치환 원칙

객체는 프로그램의 정확성을 깨뜨리지 않으면서 하위 타입의 인스턴스로 바꿀수 있어야 한다.

ISP(Interface Segregation Principle) : 인터페이스 분리 원칙

특정 클라이언트를 위한 인터페이스 여러 개가 범용 인터페이스 하나보다 낫다.

DIP(Dependency Inversion Principle) : 의존관계 역전 원칙

추상화에 의존해야지 구체화에 의존하면 안된다.( =역할에 의존한다.)

제어의 역전 IoC(Inversion of Control) 제어 흐름을 직접 제어하는 것이 아니라 외부에서 관리하는것.

의존관계 주입 DI(Dependency Injection) 정적인 클래스 의존관계와 

프레임워크 VS 라이브러리

프레임워크 내가 작성한 코드를 제어하고, 대신 실행하면 그것은 프레임워크이다.(Junit, Spring)

라이브러리 내가 작성한 코드를 직접 제어의 흐름을 담당한다면 그것은 프레임워크가 아니다.(ex. gson, poi)

스프링 컨테이너 생성

```java
ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
```

'ApplicationContext ' 를 스프링 컨테이너라고 한다.

'ApplicationContext ' 는 인터페이스 이다.

'new AnnotationConfigApplicationContext' 는 'ApplicationContext' 인터페이스의 구현체이다.

BeanFactory

- 스프링 빈을 관리하고 조회하는 역할을 담당한다.
- 스프링 컨테이너의 최상위 인터페이스이다.
- getBean() 을 제공한다.

ApplicationContext

- BeanFactory 기능을 모두 상속받아 제공한다.
- 빈을 관리하고 검색하는 기능 외에 부가기능을 제공한다.
    - 메시지소스를 활용한 다국어 기능
    - 환경변수
    - 애플리케이션 이벤트
    - 편리한 리소스 조회
    

XML 을 이용한  Bean 등록

```java
ApplicationContext ac = new GenericXmlApplicationContext("appConfig.xml");
```

new GenericXmlApplicationContext 이용하여 Bean 을 등록할수 있다. 

.Class 파일과 .XML 을 지원할수 있는 이유는 BeanDefinition 이 있기에 가능하다.

역할과 구현을 개념적으로 분리 되어있기 때문에 스프링 컨테이너는 자바코드인지 XML 인지 몰라도 된다. 

자바 코드의 경우 AnnotatedBeanDefinitionReader , XML 의 경우 XmlBeanDefinitionReader 가  읽어 들어 추상화인 BeanDefinition을 생성하여 스프링컨테이너가 사용하게 된다.

```java
public class BeanDefinitionTest {

    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);

    @Test
    void DefinitionTest(){
        String[] beanDefinitionNames = ac.getBeanDefinitionNames();
        for( String key : beanDefinitionNames){
            BeanDefinition beanDefinition = ac.getBeanDefinition(key);
            if(beanDefinition.getRole() == BeanDefinition.ROLE_APPLICATION){
                System.out.println("bean Name : " + beanDefinitionNames + " beanDefinition :" + beanDefinition);
            }
        }

    }
}
```

### 싱글톤 컨테이너

    클래스의 인스턴스가 딱 1개만 생성되는 것을 보장하는 디자인 패턴

```java
private static final SingletonService instance = new SingletonService();

    public static SingletonService getInstance() {
        return instance;
    }

    private SingletonService() {

    }
```

싱글톤 패턴을 사용하는 이유 

  - 레지스트리 설정 및 DBCP 와 같이 인스턴스 딱 1개만 존재해야 하거나 DBCP 와 같이 공통된 객체를 여러개 생성할 경우 메모리 낭비가 되고 두번째 사용시 객체 로딩 시간이 줄어 성능 향상.

인스턴스(instance) : 컴퓨터 저장공간에서 할당된 실체를 의미한다

싱글톤 패턴의 문제점

- 구현 코드가 많이 들어간다.
- 클라이언트가 구체 클래스에 의존하기에 DIP를 위반한다.
- 클라이언트가 구체 클래스에 의존해서 OCP 원칙을 위반할 가능성이 높다.
- 내부 속성을 변경하거나 초기하 하기 어렵고 자식클래스를 만들기 어렵다.

 싱글톤 컨테이너

스프링 컨테이너는 싱글톤 패턴의 문제점을 해결하면서, 객체 인스턴스를 싱글톤(1개) 으로 관리한다.

스프링 컨테이너는 싱글톤 컨테이너 역할을 한다.

싱글톤 객체를 생성하고 관리하는 기능을 싱글톤 레지스트리라 한다.

스프링 컨테이너의 기능 덕분에 위에 싱글톤 패턴에서 발생되는 단점을 해결해준다.

- 복잡한 구현코드가 들어가지 않아도 된다.
- 기존 싱글톤 패턴에서 위배되던 DIP, OCP등 위반하지 않고 자유롭게 사용가능하다.

싱글톤 컨테이너 적용후 

스프링 컨테이너의 기능 덕분에 고객의 요청이 올 때 마다 생성하는것이 아니라 이미 만들어진 객체를 공유하여 효율적으로 사용이 가능하다.

싱글톤 방식의 주의점