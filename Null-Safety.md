# Null Safety

널 안정성을 높이는 방법

- 아래와 같은 코드를 만들지 않는 방법
- 혹은 아래와 같은 널 체크를 하지 않아서 발생하는 NPE(Null Pointer Exception)을 방지하는 방법
- 완벽한 방법은 아니지만 IDE(Intellij, Eclipse)에서 경고를 표시함으로써 1차적인 문제를 방지하고, 정확한 에러 위치를 확인할 수 있도록 도움

```java
public void method(String request) {
	if(request == null) return;

	// normal process
	System.out.println(request.toUpperCase());
}
```

------

## @NonNull Annotation

- 해당 값이나 함수 등이 Null이 아님을 나타내는 어노테이션
- org.springframework.lang.NonNull 사용
- 메서드 파라미터에 붙이는 경우 : null이라는 데이터가 들어오는 것을 사전에 방지함

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bf51ba76-a104-410e-ae99-a05bd6ef5fe7/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bf51ba76-a104-410e-ae99-a05bd6ef5fe7/Untitled.png)

- 프로퍼티에 붙이는 경우 : null을 저장하는 경우 경고

  ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4ee6aee5-cee9-4f60-8d6f-76e5768daa94/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4ee6aee5-cee9-4f60-8d6f-76e5768daa94/Untitled.png)

- 메서드에 붙이는 경우 : null을 리턴하는 경우 경고, 응답값을 저장하거나 활용하는 쪽도 NonNull이라고 신뢰하고 사용

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c2e5a4a5-530a-483b-b0f0-839cbbd60502/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c2e5a4a5-530a-483b-b0f0-839cbbd60502/Untitled.png)

------

## @Nullable Annotation

- @NonNull과 반대로 해당 데이터가 null일 수 있음을 명시함
- 해당 어노테이션이 붙은 값을 사용하는 경우 null check를 항상 수행하도록 경고

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/be64dc25-c292-4ee0-8265-1dc010873b13/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/be64dc25-c292-4ee0-8265-1dc010873b13/Untitled.png)

------

## Null 관련 어노테이션 참고

- jetbrain(intellij 개발회사) : https://www.jetbrains.com/help/idea/nullable-and-notnull-annotations.html
- lombok : https://projectlombok.org/features/NonNull