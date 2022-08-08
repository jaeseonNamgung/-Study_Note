# IoC(Inversion of Control), DI(Dependency Injection)

- IoC나 DI는 레고와 같은 것이다.
  - 스프링이 바닥판처럼 깔려있고, 우리는 그 위에서 멋진 조립(나의 어플리케이션)을 만들면 된다.
  - 연결할 수 도 있고 연결을 해제 할 수 도 있는 즉 조립할 수 있는 형태이다.

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

