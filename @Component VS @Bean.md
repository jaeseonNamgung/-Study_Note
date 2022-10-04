# @Component VS @Bean

@Component 어노테이션과 @Bean 어노테이션은 둘 다 스프링 컨테이너에 빈으로 등록하기 위한 어노테이션이다.

- @Component   : @Component는 클래스 레벨에서 사용되며 런타임 시 컨포넌트 스캔을 통해 자동으로 빈으로 등록해주는 어노테이션이다.
- @Component 어노테이션은 개발자가 직접 컨트롤이 가능한 클래스를 빈으로 등록할 때 사용한다.

```java
@Component
public class Component {
   // ...
}
```

- @Bean : @Bean  어노테이션은 메서드 레벨에서 사용되며 객체를 개발자가 수동으로 스프링 컨테이너에 빈으로 등록하는 어노테이션이다.
- @Bean은 개발자가 컨트롤하기 어려운 외부 라이브러리와 같은 기능을 빈으로 등록할 때 사용된다.



```java
@Configuration
public class Config {
	@Bean
	public Sort<String> bubbleSort(){
		return new BubbleSort<>();
	}

}
```



# @Component VS @Configuration

```java
@Configuration
public class Config {
	@Bean
	public Sort<String> bubbleSort(){
		return new BubbleSort<>();
	}

}
```

```java
@Component
public class Config {
	@Bean
	public Sort<String> bubbleSort(){
		return new BubbleSort<>();
	}

}
```

두 예시를 비교해 보면 단지 클래스 레벨이 선언된 어노테이션만 다르다. @Configuration  어노테이션은 결국 @Component 어노테이션이기 때문에 같은 동작을 하게 된다. 하지만 두 어노테이션은 작은 차이가 있다.



- @Configuration : @Configuration 어노테이션은 프록시 모드로 빈을 생성하기 때문에 빈으로 등록되면 프록시 기능을 사용할 수 있게 된다.

- @Component : @Component 어노테이션은 light mode(경량모드) 이기 때문에 프록시 빈으로 등록이 안되고 그대로 new 생성자로 생성되기 때문에 팩토리 메서드와 가깝게 동작한다. 즉 두 번 선언되면 두 번 인스턴스가 생성되고 세 번 선언되면 세번 인스턴스가 생성된다.

  