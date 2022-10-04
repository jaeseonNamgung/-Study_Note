# 빌더 패턴 (Builder Pattern) 이란?

빌더 패턴은 복잡한 객체를 생성하는 방법을 정의하는 클래스와 표현하는 방법을 정의하는 클래스를 별도로 분리하여, 서로 다른 표현이라도 이를 생성할 수 있는 동일한 절차를 제공하는 패턴이다.



### 생성자를 사용할 경우

```java
Member member = new Member("홍길동", "id", "pwd", "test@email.com");
```

생성자를 통해 객체를 생성할 경우 여러가지 단점이 있다.

1. 생성자 파라미터가 많을 경우 가독성이 좋지 않다.
   - 생성자의 파라미터가 많을 경우 이 값이 어떤 값인지 이해하기 힘들어진다.

2. 생성자 파라미터의 순서를 반드시 지켜야한다.
   - 생성자 파라미터의 순서를 지키지 않을 경우 오류가 발생한다.

### 빌더패턴을 사용할 경우

```java
Member member = Member.builder()
				.name("홍길동")
    			.id("id")
    			.pwd("pwd")
    			.email("test@email.com")
    			.build();
```

빌더패턴을 사용할 경우 가독성도 좋아질 뿐만 아니라 순서의 상관없이 파라미터를 설정할 수 있다.



### @Builder 어노테이션

```java
@Builder
public class Member{
    private String name;
    private String id;
    private String pwd;
    private String email;                 
}
```

- 주의사항

  ```javja
  @NoArgsConstructor
  @AllArgsConstructor 
  ```

@Builder 어노테이션을 사용하기 위해서는 위 두 개의 어노테이션을 사용해야 한다. 

### @Builder 속성

1. builderMethodName

   @Builder 어노테이션을 사용하면 빌더를 생성하는 메서드의 이름은 기본 값인 builder()이다. 이를 새롭게 네이밍 할 수 있는 어노테이션 속성이다. (Default : builderr())

2. builderClassName

   @Builder를 사용하면 각 필드들의 값을 셋팅하는 메서드의 반환값의 필더 클래스의 이름을 ...Builder로 자동 설정된다. 예를 들어 클래스 이름이 Member일 경우 @Builder의 적용된 반환 값이 MemberBuilder가 된다. 이걸 사용자가 원하는 대로 네이밍 하고 싶을때 `builderClassName` 속성을 사용한다.

3. toBuilder

   boolean 값으로 설정할 수 있는 어노테이션으로 기본 값은 false이다. 이 값을 true로 설정 시 빌더로 만든 인스턴스에서 toBuilder() 메서드를 호출해 그 인스턴스 값을 베이스로 빌더 패턴으로 새로운 인스턴스를 생성할 수 있다. 기본값인 fasle로 설정할 경우 toBuilder() 메서드 자체가 존재하지 않을 경우 에러가 발생한다.

   ```java
   Member member1 = Member.builder()
   				.name("홍길동")
       			.id("id")
       			.pwd("pwd")
       			.email("test@email.com")
       			.build();
               
   Member member2 = Member.toBuilder().name("홍길동").build();
   ```

4.  access()

   builder 클래스의 접근제어자를 어떻게 할 것인지에 대한 설정 값이다. (Default : public)

```java
AccessLevel access() default lombok.AccessLevel.PUBLIC;
```

