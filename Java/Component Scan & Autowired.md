# Component Scan & Autowired

<br/>

## 1. Component Scan

<br/>

Component Scan 을 사용하기 전에는 AppConfig 에 @Bean 등을 통해서 설정 정보에 직접 등록할 스프링 빈을 나열했었다. 하지만 실제 서비스의 경우 수백개의 빈이 사용되면서 설정 정보가 너무 커지고 누락하는 가능성도 많고 매번 등록해야하는 번거로움이 많았다.

스프링은 자동으로 스프링 빈을 등록하는 “Component Scan” 이라는 기능을 제공하면서 기존의 문제점들을 해소했다.

<br/>

```java
@Configuration
@ComponentScan
public class AppConfig {

}
```

<br/>

@ComponentScan 어노테이션을 써주면 최초 등록시 자동으로 스캔들 시작한다.

말 그대로 @Component 어노테이션이 붙은 Bean 들을 스캔하는 기능이다.

<br/>
<br/>

### 2. Autowired

<br/>

```java
@Component
public class ServiceImpl implements Service {

		private final Repository repository;


		@Autowired
		public ServiceImpl(Repository repository) {
				this.repository = repository;
		}

}
```

<br/>

@Component 를 타고 스캔하고나면 의존관계 주입도 자동으로 해주는데

Component 스캔 하면서 Bean 들이 등록된 후 @Autowired 가 실행되며 생성자 주입을 실행한다.

이때 Constructor 에 들어오는 파라미터에 명시된 타입 (Rpository) 과 같은 것을 최초 스캔 당시 올라와 있는 Bean 의 타입과 일치하는 것을 찾아 자동으로 의존관계를 주입해준다. getBean() 메소드와 같은 기능을 자동으로 해준다고 생각하면 된다.

@ComponenScan 은 Component 뿐만 아니라 Controller, Service , Repository, Configuration 도 탐색 범위인데 이것은 각 어노테이션을 들여다보면 @Component 가 각각 붙여져있기 떄문이다.

만약 같은 타입이 2개 이상 발견 된다면? 탐색된 이름이 제일 유사한 것 먼저 사용하게 된다.

그래서 컨테이너에 등록될 이름을 컬대 중복되지 않게 설계하는 것이 중요하다.
