# IoC(Inversion of Control), DI(Dependency Injection)

- IoC나 DI는 레고와 같은 것이다.
  - 스프링이 바닥판처럼 깔려있고, 우리는 그 위에서 멋진 조립(나의 어플리케이션)을 만들면 된다.
  - 연결할 수 도 있고 연결을 해제 할 수 도 있는 즉 조립할 수 있는 형태이다.

## 의존성 주입(Dependency Injection)이란

의존성 주입이란 한 객체가 다른 객체를 사용할 때 의존한다라고 한다. 예를 들어 A라는 객체안에서 B라는 객체를 사용할 경우 A는 B를 의존한다고 한다.

![img](https://blog.kakaocdn.net/dn/tPofw/btqGxlQlWZ6/eWiKNgMCe0BGlVkz5JKDI0/img.png)

출처 : https://m.blog.naver.com/PostView.nhn?blogId=ljh0326s&logNo=221395815870&proxyReferer=https:%2F%2Fwww.google.com%2F



### 강한 결합과 느슨한 결합?

```java
public class Store {

    private Pencil pencil;

    public Store() {
        this.pencil = new Pencil();
    }

}

```

출처: https://mangkyu.tistory.com/150 [MangKyu's Diary:티스토리]

- 강한 결합 : 위에 코드처럼 되어있는 걸 강한 결합이라고 한다 . 클래스의 이름처럼 Store은 여러 상품들이 있는 클래스이다. 하지만 store 클래스에 직접 penciil 클래스를 결합할 경우 강하게 결합되어 있기 때문에 다른 상품을 교체하기 위해서는 Stroe 클래스의 생성자에 변경이 필요하다.   즉 여러 상품들중 Store 클래스 상점은 하나만 존재하면 되기 때문에 여러번 중복돼서 생성되는 것은 옳지 않다.

```java
public interface Product {

}

public class Pencil implements Product {

}

```

출처: https://mangkyu.tistory.com/150 [MangKyu's Diary:티스토리]

```java
public class Store {

    private Product product;

    public Store(Product product) {
        this.product = product;
    }

}
```

출처: https://mangkyu.tistory.com/150 [MangKyu's Diary:티스토리]

- 느슨한 결합 : 느슨한 결합은 위에 강한 결합에 반대 개념이다. Store 클래스는 한 번만 생성되고  상품만 교체되는 방식이다.

  위에 코드 처럼 하나의 Product 인터페이스를 이용해 Product 내에서 상품을 교체하는 방식을 사용해 코드를 느슨하게 만들어 주는 것이다.  **Product를 외부에서 상품을 주입(Injection)이라고 한다.** 이렇게 하면 결합도를 낮출 수 있고, 런타임시에 의존 관계가 결정되기 때문에 유연한 구조를 가진다.

여기서 Spring이 DI 컨테이너를 필요로 하는데 애플리케이션 실행 시점에 필요한 객체(빈)를 생성해야 하며, 필요한 객체를 자동으로 연결할 수 있는 하나의 컨테이너가 필요하다.

```java
public class BeanFactory {

    public void store() {
        // Bean의 생성
        Product pencil = new Pencil();
    
        // 의존성 주입
        Store store = new Store(pencil);
    }
    
}
```

출처: https://mangkyu.tistory.com/150 [MangKyu's Diary:티스토리]

## 의존성 주입을 사용하는 이유

1. 재사용성을 높여준다.

2. 테스트에 용이하다.

3. 코드를 단순화 시켜준다.

4. 사용하는 이유를 파악하기 수월하고 코드가 읽기 쉬워지는 점이 있다.

5. 종속성이 감소하기 때문에 변경에 민감하지 않다.

6. 결합도(coupling)는 낮추면서 유연성과 확장성은 향상 시킬 수 있다.

7. 객체간의 의존관계를 설정할 수 있다.

## Bean이란

- 자바에서의 javaBean 

  - 자바빈 규약 또는 자바빈 관례에 따라 만들어진 클래스를 의히한다.
  - 데이터를 저장하기 위한 구조체로 자바 빈 규약이라는 것을 따르는 구조체 
  - private 프로퍼티와 getter/setter로만 데이터를 접근한다.

   

#### 자바빈 규약

1. 패키지 - 자바빈은 기본(Default) 패키지의 이외의 특정 패키지에 속해 있어야 한다. -> default 패키지에서 클래스 생성 X
2. 기본 생성자가 존재해야 한다.
3. 멤버변수의 접근제어자는 private로 선언되어야 한다.
4. 멤버변수에 접근 가능한 getter와 setter 메서드가 존재해야 한다.
5. getter와 setter는 접근자가 public으로 선언되어야 한다.

```java
package bean; // 패키지 지정

public class JavaBean {
	
	public JavaBean() {  // 기본 생성자 생성 -> 없어도 됨
		// TODO Auto-generated constructor stub
	}
	private int number; // private 지정
	private String name;
	public int getNumber() { // getter 지정
		return number;
	}
	public void setNumber(int number) { // setter 지정
		this.number = number;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}

}
```



​	

- 스프링에서의 bean

  - 스프링 IoC 컨테이너에 의해 생성되고 관리되는 객체

  - 자바에서처럼 new Object();로 생성하지 않는다.

  - 각각의 Bean들 끼리는 서로를 편리하게 의존 (사용) 할 수 있다.

    ​	

  

## 스프링 컨테이너 개요

ApplicationContext 인터페이스를 통해 제공되는 스프링 컨테이너는 Bean 객체의 생성 및 Bean들의 조립(상호 의존성 관리)을 담당한다.

![spring_context](https://raw.githubusercontent.com/jaeseonNamgung/img_repo/main/img/spring_context.png)

- bean의 등록

  - 과거에는 xml로 설정을 따로 관리하여 등록(불편)
  - 현재는 annotation 기반으로 Bean 등록
    - @Bean, @Controller, @Service

  - Bean 등록 시 정보
    - Class 경로
    - Bean의 이름
      - 기본적으로는 원 Class 이름에서 첫 문자만 소문자로 변경 -> accountService, userDao
      - 원하는 경우 변경 가능
    - Scope : Bean을 생성하는 규칙
      	- singleton : 컨테이너에 단일로 생성
      	- prototype : 작업 시마다 Bean을 새로 생성하고 싶을 경우
      	- request : http 요청마다 새롭게 Bean을 생성하고 싶은 경우

  -  Bean LifeCycle callback
    - callback : 어떤 이벤트가 발생하는 경우 호출되는 메서드
    - lifecycle callback
      - Bean을 생성하고 초기화하고 파괴하는 등 특정 시점에 호출되도록 정의된 함수
    - 주로 많이 사용되는 콜백
      - @PostConstruct :  빈 생성 시점에 필요한 작업을 수행
      - @PreDestroy : 빈 파괴(주로 어플리케이션 종료) 시점에 필요한 작업을 수행



#### singleton

소프트웨어 디자인 패턴에서 싱글톤 패턴을 따르는 클래스는, 생성자가 여러 차례 호출되더라도 **실제로 생성되는 객체는 하나**이고 최초 생성 이후에 호출된 생성자는 최초의 생성자가 객체를 리턴한다.

- singleton을 사용하는 이유
  - 만약 우리가 만들었던 DI 컨테이너인 요청을 할 때마다 새로운 객체를 생성한다. 요청이 엄청나게 많은 트래픽 사이트에서는 계속 객체를 생성하게 되면 **메모리 낭비가 심하**기 때문이다.

#### prototype 

- prototype은 요청을 할 때마다 새로운 객체를 반환한다.
- 빈을 생성하고, DI하고, 초기화 까지만 하고 클라이언트에게 넘긴다.

### Bean 등록 방법

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class ApplicationConfig {

    @Bean
    public BookRepository bookRepository() {
        return new BookRepository();
    }

    @Bean
    public BookService bookService() {
        BookService bookService = new BookService();
        bookService.setBookRepository(bookRepository());
        return bookService;
    }
}
```

출처 : https://devlog-wjdrbs96.tistory.com/165

위에 코드 처럼 ApplicationConfig 클래스를 하나 생성하고 어노테이션으로 **@Configuration** 클래스 이릠 위해 작성해준다.

그런 다음 빈으로 등록할 객체에  **@Bean**  어노테이션을 작성해주면 된다.



- 여러 빈 등록 방법

```
ex) @Bean, @Component, @Controller, @Service, @Repository 

- @Bean은 개발자가 컨트롤 할 수 없는 외부 라이브러리 Bean으로 등록하고 싶은 경우 (메소드로 return 되는 객체를 Bean으로 등록) 
- @Component는 개발자가 직접 컨트롤할 수 있는 클래스(직접 만든)를 Bean으로 등록하고 싶은 경우 (선언된 Class를 Bean으로 등록) 
- @Controller, @Service, @Repository 등 은 @Component를 비즈니스 레이어에 따라 명칭을 달리 지정해준 것Container에 있는 Spring Bean을 찾아 주입시켜주는 Annotation
```

출처 : https://devlog-wjdrbs96.tistory.com/165



### 다양한 의존성 주입 방법

1. 생성자 주입 ( 스프링 4.0 부터 생성자 주입 사용 권장 )

```java
@Service
public class UserService {

    private UserRepository userRepository;
    private MemberService memberService;

    @Autowired // 생략가능
    public UserService(UserRepository userRepository, MemberService memberService) {
        this.userRepository = userRepository;
        this.memberService = memberService;
    }
    
}
```

출처: https://mangkyu.tistory.com/125 [MangKyu's Diary:티스토리]

생성자는 클래스가 생성될 때 딱 한번만 실행된다. 그렇기 때문에 **주입받는 객체가 변하지 않거나, 반드시 객체의 주입이 필요한 경우에 강제 하기 위해 사용할 수 있다.**  생성자 주입은 클래스내에 생성자가 하나만 선언되어 있을 경우  @Autowired 어노테이션을 사용하지 않아도 자동으로 주입이 가능하다.

2. setter 주입

```java
@Service
public class UserService {

    private UserRepository userRepository;
    private MemberService memberService;

    @Autowired
    public void setUserRepository(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    @Autowired
    public void setMemberService(MemberService memberService) {
        this.memberService = memberService;
    }
}
```

출처: https://mangkyu.tistory.com/125 [MangKyu's Diary:티스토리]

setter 주입은 setter 메서드를 통해 의존 주입하는 방법이다. setter 주입은 주입 받는 객체가 변결될 수있다는 특징이 있다.

또한 @Autowired가 없을 경우 오류가 발생한고 주입할 대상이 없어도 오류가 발생한다. 

-> 주입할 대상이 없어도 동작하도록 하려면 @Autowired(required = false)를 통해 설정할 수 있다.



3. 필드 주입

```java
@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;
    @Autowired
    private MemberService memberService;

}
```

출처: https://mangkyu.tistory.com/125 [MangKyu's Diary:티스토리]

필드 주입은 바로 필드에 의존 주입을 하는 방식이다. 이렇게 될 경우 **코드가 짧아지고 사용하기도 편해지지만 객체를 수정할 수 없고  외부에서 접근이 불가능하기 때문에 테스트 코드 작성이 불가능하다**. 즉 사용을 지양해야 하는 코드이다.
