# Ioc 란?

### Ioc : 제어의 역전 ( Inversion of Control )

기존의 프로그램은 클라이언트 구현 객체가 스스로 필요한 서버 구현 객체를 생성하고, 연결, 실행하게 만들었고 한마디로 구현 객체가 프로그램의 제어 흐름을 스스로 조종했다.

하지만 객체 지향의 5원칙 , 유지 보수 관점에서 어려움이 많았고 이를 해결하기 위해 “객체의 생성과 관리를 개발자가 아닌 프레임워크가 담당”하게 하여 프로그램 외부에서 제어 할 수 있게 만들기 시작했다. 이로써 개발자가 객체의 생성과 관리를 직접 하지 않아도 되므로 코드의 복잡성을 줄이고, 유지보수성과 확장성을 높이는 데 효과적이였고 결과적으로 개발자는 비지니스 로직에만 집중할 수 있게 되었다.

# DI 란?

<br/>
<br/>

### DI : 의존관계 주입 ( Dependency Injection )

<br/>

DI는 의존성 주입이라는 의미로, 객체가 필요로 하는 의존성을 외부에서 주입해주는 것이다. 이는 객체 간의 결합도를 낮추고, 코드의 재사용성을 높이는 데 도움이 되어 유지보수성과 확장성을 높일 수 있다.

<br/>
<br/>

### < 클래스 다이어그램 >

<br/>
<br/>

![스크린샷 2024-07-14 오전 11.35.33.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/2fb4e0b6-2f67-42d5-ae18-318abefb8cc9/e0389eac-fb96-4a93-a711-a958f25c9506/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2024-07-14_%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB_11.35.33.png)

<br/>
<br/>

```java
public static class AppConfig {
			// service 라는 메소드를 통해 impl이라는 구현 객체를 선택해 주입하며
			// serviceImpl 에는 repository() 라는 메소드를 주입한다.

			public Service service() {
	        return new ServiceImpl(repository());
	}
			// repository() 메소드 를 통해 RepositoryType1 이라는 구현 객체를 선택해 의존관계를 주입한다.
	    public Repository repository() {
	        return new RepositoryType1();
	}

}
```

<br/>
<br/>

Ioc 와 DI 를 이루기 위해 AppConfig 라는 class 를 생성해 의존 관계 주입을 해준다.

그러면 ServiceImpl 입장에서는 RepositoryType1 이 주입 될지 RepositoryType2 가 주입 될지는 전혀 모른다.

이렇게 코드 간의 결합도를 낮추며 제어권을 프레임 워크가 가져가게 설계할 수 있다.

여기서 만든 AppConfig 라는 클래스는 Ioc 컨테이너 혹은 DI 컨테이너 라고도 불리며 객체 생성과 의존 관계를 연결해주는 담당으로 사용된다.
