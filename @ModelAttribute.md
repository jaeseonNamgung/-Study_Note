# @ModelAttribute 어노테이션을 사용하기 위한 방법

@ModelAttribute는 클라이언트에서 전달된 값들을 자동으로 바운딩 해주고 받은 값을 자동으로 클라이언트에 보내주는 어노테이션입니다. @ModelAttribute를 사용하기 위해서는 몇 가지의 규칙을 조심해야 합니다.
1. @ModelAttribute 어노테이션을 이용해 객체에 값을 바운딩하기 위해서는 자바빈 규명을 따라야 합니다.
2. 클라이언트에 타입과 컨트롤러의 타입이 일치해야만 합니다.

자바빈즈의 규칙은 아래와 같습니다.
1. 멤버 변수의 접근 제한자는 **private**이여야 합니다.
2. 기본 생성자가 정의 되어 있어야 합니다.
3. 모든 멤버 변수에는 **getter/setter** 메서드가 정의 되어 있어야 합니다.
4. **getter/setter** 메서드의 접근 제한자는 **public**이여야 합니다. 

```java
@Setter
@Getter
public class UserVO {
	
	private String id;
	private String password;
	private String userName;
}
```
먼저 UserVO 클래스에 id, password userName 변수를 선언해 주고 gettter 메서드와 setter 메서드를 만들어줘야 합니다. 저는 lombok을 이용해서 getter/setter 메서드를 정의했습니다.
자바빈즈 규칙을 위히서 변수의 **private** 설정과 **getter/setter** 메서드 그리고 **기본 생성자**는 무조건 작성해 줘야 합니다.  
```java
@Controller
@RequestMapping("/user/")
public class UserController {
	
	@GetMapping("/get")
	public void get() {}
	
	@GetMapping("/page")
	public void test(@ModelAttribute UserVO user) {
		
	}
}

```
컨트롤러에는 최대한 간단히 작성했습니다. get.jsp로 가기 위한 메서드를 하나 만들고 get.jsp에서는 단순히 아이디, 비밀번호, 이름을 입력하고 form 태그를 이용해 /user/page url로 정의한 메서드로 데이터들을 보내줍니다. 여기서 우리는 @ModelAttribute 어노테이션을 사용했습니다. 그렇기 때문에 UserVO 객체에는 자동으로 객체가 생성되며 UserVO 객체에 값이 바인딩 되게 됩니다. (자바빈즈의 규약에 부합하기 때문에) 그리고 @ModelAttribute 어노테이션을 자동으로 클라이언트에 데이터를 보내줍니다 즉 우리가 따로 request로  보내주지 않아도 됩니다. 만약 @ModelAttribute에 따로 네이밍을 작성하지 않으면 UserVO 객체에 첫 글자를 소문자로 변경돼서 값이 전달되고 @ModelAttribute("user")처럼 네이밍을 따로 작성하면 user로 값이 전달됩니다.

![image](https://user-images.githubusercontent.com/84066249/158988059-85cdb8de-8783-4db0-85e1-442872a58194.png)

page.jsp로 값을 전달했을 때 결과 값입니다 이 처럼 @ModelAttribute를 사용하면 코드량을 줄일 수 있고 반복적인
코드도 줄일 수 있습니다. 


