# Validation in Spring

### Validation 이란?

- 유효성 검사
- 주로 사용자 또는 요청(http request) 내용에서 잘못된 내용이 있는지 확인하는 단계를 뜻한다.

### Validation의 종류

학문적으로 여러 세부적인 단계들이 있기도 하지만 실제로 개발자가 주로 챙겨야 하는 검증은 크게 두 종류로 나뉜다.

###### 데이터 검증

- 필수 데이터의 존재 유무
- 문자열의 길이나 숫자형 데이터의 경우 값의 범위
- email, 신용카드 번호 등 특정 형식에 맞춘 데이터

###### 비즈니스 검증

- 서비스 정책에 따라 데이터를 확인하여 검증
- 예) 배달앱인 경우 배달 요청을 할 때 해당 주문건이 결제 완료 상태인지 확인 등
- 경우에 따라 외부 API를 호출하거나 DB의 데이터까지 조회하여 검증하는 경우도 존재

### Spring의 Validation

스프링은 웹 레이어에 종속적이지 않은 방법으로 Validation을 하려고 의도하고 있으며 주로 아래 두가지 방법을 활용하여 Validation을 진행

###### Java Bean Validation

Java Bean 기반으로 간편하게 개별 데이터를 검증

요즘에 가장 많이 활용되는 방법 중 하나이며, 아래 코드처럼 JavaBean 내에 어노테이션으로 검증방법을 명시함

```java
public class MemberCreationRequest {
		@NotBlank(message="이름을 입력해주세요.")
		@Size(max=64, message="이름의 최대 길이는 64자 입니다.")
    private String name;
		@Min(0, "나이는 0보다 커야 합니다.")
    private int age;
		@Email("이메일 형식이 잘못되었습니다.")
    private int email;

    // the usual getters and setters...
}
```

위처럼 요청 dto에 어노테이션으로 명시 후 아래처럼 @valid 어노테이션을 해당 @RequestBody에 달게 되면, Java Bean Validation을 수행한 후 문제가 없을 때만 메서드 내부로 진입이 된다.

- 검증 중 실패가 발생하면? : MethodArgumentNotValidException이 발생

```java
@PostMapping(value = "/member")
public MemeberCreationResponse createMember(
	@Valid @RequestBody final MemeberCreationRequest memeberCreationRequest) {
	// member creation logics here...
}
```



### Spring Validator 인터페이스 구현을 통한 validation

```java
public class Person {

    private String name;
    private int age;

    // the usual getters and setters...
}
```

위처럼 Person이라는 JavaBean 객체가 있을 때, 아래는 해당 인스턴스에서만 활용되는 Validation이다.

인터페이스에 있는 두개의 메서드는 아래와 같은 역할을 한다.

- supports : 이 validator이 동작할 조건을 정의, 주로 class의 타입을 비교
- validate : 원하는 검증을 진행한다.

```java
public class PersonValidator implements Validator {

    /**
     * This Validator validates only Person instances
     */
    public boolean supports(Class clazz) {
        return Person.class.equals(clazz);
    }

    public void validate(Object obj, Errors e) {
        ValidationUtils.rejectIfEmpty(e, "name", "name.empty");
        Person p = (Person) obj;
        if (p.getAge() < 0) {
            e.rejectValue("age", "negativevalue");
        } else if (p.getAge() > 110) {
            e.rejectValue("age", "too.darn.old");
        }
    }
}
```



### Validation 수행 시 주의상항 및 패턴

###### 주의사항

- validation이 너무 여러 군데에 흩어져 있으면 테스트 및 유지보수성이 떨어짐
  - 중복된 검증 : 정책 변경 시에 모든 코드를 수정해야 한다.
  - 다른 검증 : 여러 군데서 다른 정책을 따르는 검증이 수행될 수 있음
- Spring Validator의 장단점
  - 장점 : Java Bean Validation에 비해 조금 더 복잡한 검증이 가능하다.
  - 단점
    - Validation을 수행하는 코드를 찾기가 (상대적으로) 어렵다.
    - 완전히 데이터만 검증하는 것이 아니기 때문에 일부 비즈니스적인 검증이 들어가는 경우가 있다.
      - 이 경우 비즈니스 검증 로직이 여러 군데로 흩어지기 때문에 잘못된 검증(중복 검증, 다른 정책을 따르는 검증)을 수행할 가능성이 높아진다.

# Data Binding

사용자나 외부 서버의 요청 데이터를 특정 도메인 객체에 저장해서 우리 프로그램에 Request에 담아주는 것을 뜻한다.



### Converter<S, T> Interface

S (Source)라는 타입을 받아서 T (Target)이라는 타입으로 변환해주는 Interface, 인터페이스의 모양은 아래와 같다

```java
package org.springframework.core.convert.converter;

public interface Converter<S, T> {

    T convert(S source);
}
```

- 파라미터에 json 형식 문자열이 담겨오는 경우 해당 문자열을 곧바로 특정 dto에 담고 싶을 때 사용

```java
// 요청
GET /user-info
x-auth-user : {"id":123, "name":"Paul"}

// 유저 객체
public class XAuthUser {
    private int id;
    private String name;

    // the usual getters and setters...
}

@GetMapping("/user-info")
public UserInfoResponse getUserInfo(
	@RequestHeader("x-auth-user") XAuthUser xAuthUser){

	// get User Info logic here...
}
```

위 처럼 헤더에 담긴 json 형식 문자열을 XAuthUser에 바로 담고 싶은 경우 아래와 같이 Converter를 Bean으로 등록하면 된다.

```java
@Component
public class XAuthUserConverter implements Converter<String, XAuthUser> {
	@Override
	public XAuthUser convert(String source) {
		return objectMapper.readValue(source, XAuthUser.class);
	}
}
```

이와 비슷하게 PathParameter나 기타 특수한 경우의 데이터를 특정 객체에 담고 싶은 경우

- Converter를 만들어서 Spring에 Bean으로 등록
- 스프링 내에 ConversionService라는 내장된 서비스에서 Converter 구현체 Bean들을 Converter 리스트에 등록
- 외부데이터가 들어오고, Source Class Type → Target Class Type이 Converter에 등록된 형식과 일치하면 해당 Converter가 동작하는 원리

## Formatter

특정 객체 ↔ String간의 변환을 담당

아래 샘플 코드는 Date ↔ String 간의 변환을 수행하는 Formatter이다.

- print : API 요청에 대한 응답을 줄 때, Date형식으로 된 데이터를 특정 locale에 맞춘 String으로 변환
- parse : API 요청을 받아올 때, String으로 된 "2021-01-01 13:15:00" 같은 날짜 형식의 데이터를 Date로 변환하도록 함

```java
package org.springframework.format.datetime;

public final class DateFormatter implements Formatter<Date> {
    public String print(Date date, Locale locale) {
        return getDateFormat(locale).format(date);
    }

    public Date parse(String formatted, Locale locale) throws ParseException {
        return getDateFormat(locale).parse(formatted);
    }
		// getDateFormat 등 일부 구현은 핵심에 집중하기 위해 생략... 
}
```

Formatter도 Converter와 마찬가지로 Spring Bean으로 등록하면 자동으로 ConversionService에 등록시켜주기 때문에 필요(요청/응답 시 해당 데이터 타입이 있는 경우)에 따라 자동으로 동작하게 된다. 