# Spring 컨테이너 & Singleton

<br/>

## Spring 컨테이너

AppConfig 라는 클래스를 통해 DI 컨테이너를 만들어서 제어를 프레임워크에게 할당시켜서 사용했다.

<br/>


```java
ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
```

<br/>

spring 프레임워크는 AppConfig 와 같은 설정정보를 ApplicationContext 라는 spring 컨테이너에 주입시켜 사용한다. 또다른 방법으로는 XML 파일을 따로 생성하여 AppConfig 와 같은 역할로 사용했다.

<br/>

![singleton1](https://github.com/user-attachments/assets/e296adfb-a3a1-4f0a-a737-916af403b9b2)

<br/>

1. ApplicationContext 를 생성함과 동시에 컨테이너가 생성이 된다.
2. 스트링 컨테이너에 넣은 설정정보(AppConfig) 를 훑어보고 Bean 으로 등록
3. getBean(beanName, beanType) 을 입력하여 사용한다.

<br/>
<br/>

## Singleton

<br/>


서비스를 만들어 배포하고 나면 여러 유저들이 여러 요청을 하게 된다. 그러면 클라이언트가 서버로 요청을 할때마다 로직에 사용되는 객체 인스턴스를 새로 생성해야한다. 이러면 메모리 낭비가 되고 과도한 요청이 몰리면 서버는 다운되게 된다.

<br/>
<br/>

### Singleton 이란?

<br/>


그래서 이러한 문제를 해결하기 위해 singleton 이라는 개념이 등장했다.

특정 객체의 인스턴스를 최초 호출 시 1개만 생성하고 2개 이상 생성하지 못하게 만들고 그 다음의 요청에서 인스턴스를 공유하는 패턴을 말한다.

이를 통해 메모리 낭비 문제를 방지할 수 있다.

<br/>
<br/>

```java
public class SingletonService {

//1. static 영역에 객체를 딱 1개만 생성해둔다.
private static final SingletonService instance = new SingletonService();

     //2. public으로 열어서 객체 인스턴스가 필요하면 이 static 메서드를 통해서만 조회하도록 허용한 다.
     public static SingletonService getInstance() {
         return instance;
     }

     //3. 생성자를 private으로 선언해서 외부에서 new 키워드를 사용한 객체 생성을 못하게 막는다.
     private SingletonService() {}

     public void logic() {
         System.out.println("싱글톤 객체 로직 호출");
     }
}
```

<br/>

1. static 영역에 객체 instance를 미리 하나 생성해서 올려둔다.
2. 이 객체 인스턴스가 필요하면 오직 `getInstance()` 메서드를 통해서만 조회할 수 있다. 이 메서드를 호
   출하면 항상 같은 인스턴스를 반환한다.
3. 딱 1개의 객체 인스턴스만 존재해야 하므로, 생성자를 private으로 막아서 혹시라도 외부에서 new 키워드
   로 객체 인스턴스가 생성되는 것을 막는다.

<br/>
<br/>

### Singleton 의 문제점

<br/>

싱글톤 패턴은 구현하는 코드가 많다.

의존관계상 클라이언트가 구체 클래스에 의존한다. → DIP를 위반한다. / OCP 원칙을 위반할 가능성이 높다.
테스트하기 어렵다.
내부 속성을 변경하거나 초기화 하기 어렵다.
private 생성자로 자식 클래스를 만들기 어렵다.
결론적으로 유연성이 떨어져 안티패턴으로 불린다.

<br/>
<br/>

### Singleton 컨테이너

<br/>

스프링 프레임워크는 자체적으로 다른 코드를 장성하지 않아도 등록된 Bean 들을 싱글톤으로 관리해준다.

이로써 DIP, OCP, 테스트, private 생성자로 부터 자유롭게 싱글톤을 사용할 수 있다.

<br/>


![singleton2](https://github.com/user-attachments/assets/e87677c2-77ec-4ec8-b10d-d3157f3b594f)

<br/>
<br/>

### Singleton 패턴의 주의점

<br/>


여러 클라이언트가 인스턴스를 공유해서 사용하다보니 싱글톤 객체를 유지(stateful)하게 설계하면 안되고

무상태(stateless) 로 설계해야 한다.

무상태라하면 특정 클라이언트에 의존적인 필드가 있으면 안되며 특정 클라이언트가 값을 변경할 수 있는 필드가 있으면 안된다.

가급적 읽기만 가능해야하고 필드 대신에 자바에서 공유되지 않는 지역변수, 파라미처 threaLocal 등을 사용해야한다.

스프링 빈의 필드에 공유값을 설정하면 장애가 발생한다.

<br/>
<br/>

### @Configuration

<br/>


```java
@Configuration
public class AppConfig {
	@Bean
	public void method1() {}
	@Bean
	public void method2() {}
	@Bean
	public void method3() {}
}
```

<br/>
<br/>

이처럼 AppConfig 에 @Configuration 어노테이션을 달아놓으면 안에 Bean 뿐만 아니라 AppConfig.class 자체도 스프링 컨테이너에 등록 되기 때문에 new AnnotationConfigApplicationContext(AppConfig.class) 를 했을 시점에 이미 인스턴스가 생성되어 이후부터 공유되시 시작된다.
