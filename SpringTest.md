# SpringTest

## Mockito 란

- Mock : Mock 객체는 가짜(모의)라는 뜻을 가지며 테스트를 진행할 때 진짜 객체와 비슷하게 동작하도록 프로그래머가 직접 행동을 관리하는 객체이다. 객체를 쉽게 만들고, 관리하고, 검증할 수 있는 방법을 제공하는 프레임워크이다.

### Mockito 사용하기 위한 방법

```java
@ExtendWith(MockitoExtension.class)
// @ExtendWith는 외부 기능을 사용할 때 지정하는 어노테이션이다.
class DMakerServiceTest {
    @Mock // 객체를 만들어 반환해주는 가짜 객체 어노테이션
    private DeveloperRepository developerRepository;
    @Mock
    private RetiredDeveloperRepository retiredDeveloperRepository;

    @InjectMocks 
    // 가짜 객체를 자동으로 주입해주는 어노테이션 (가짜 Autowired와 같다)
    // @Mock 어노테이션으로 객체를 생성한 객체를 @InjectMocks 어노테이션에 생성된 객체에 주입해주는 기능
    private DMakerService dMakerService;

    @Test
    // @Test : Test 실행 부분
    void testSomeThing(){
		
        // given
        given(developerRepository.findByMemberId(anyString())) // anyString() : null이 아닌 임의의 문자열
                .willReturn(Optional.of(Developer.builder() 
       // willReturn() : given 에서 받아온 객체에 값을 넣을 때 사용한다.
       // Optional.of : null이 아닌 값을 담을 때 사용한다. 만약 null 값이 들어오면 NullPointException이 발생한다. 
                                .developerLevel(DeveloperLevel.SENIOR)
                                .developerSkillType(DeveloperSkillType.FRONT_END)
                                .experienceYears(12)
                                .statusCode(StatusCode.EMPLOYED)
                                .name("name")
                                .age(40)
                                .build()));
        // when
        DeveloperDetailDto developerDetail =  dMakerService.getDeveloperDetail("memberId");

        // then
        assertEquals(DeveloperLevel.SENIOR, developerDetail.getDeveloperLevel());
        assertEquals(DeveloperSkillType.FRONT_END, developerDetail.getDeveloperSkillType());
        assertEquals(12, developerDetail.getExperienceYears());
        System.out.println(developerDetail.getDeveloperLevel());
        
    }
}
```



### Given - When - Then

- Given
  - 테스트를 위한 주어진 상태
  - 테스트를 대상에게 주어진 조건
  - 테스트가 동작하기 위해 주어진 환경
- When
  - 테스트를 대상에게 가해진 어떠한 상태
  - 테스트 대상에게 주어진 어떠한 조건
  - 테스트 대상의 상태를 변경 시키기 위한 환경
- Then
  - 앞선 과정의 결과

즉, 어떤 상태에서 출발(given)하여 어떤 상태의 변화를 가했을 때(when) 기대하는 어떠한 상태가 되어야 한다.(then)



### Assert

- Assert는 if문을 줄이는 역할을 하며, 프로젝트 규칙을 적용하고 재 사용 목적으로 사용된다.

|             메서드             |                             설명                             |
| :----------------------------: | :----------------------------------------------------------: |
|       assertEquals(x,y)        |    객체 x와 y가 일치하는지 판단 (true일 경우 테스트 통과)    |
|    assertArrayEquals(a, b)     |                 배열 A와 B가 일치하는지 판단                 |
|         assertFalse(x)         |                      x가 false인지 판단                      |
|         assertTrue(x)          |                      x가 true인지 판단                       |
| assertTrue(message, condition) |             condition이 true이면 message를 출력              |
|        assertNull(obj)         |                   객체 obj이 null인지 판단                   |
|       assertNotNull(obj)       |                객체 obj이 null이 아닌지 판단                 |
|     assertSame(objA, objB)     | 객체 objA와 objB가 같은 객체인지 판단<br />(같으면 테스트 통과,assertEquals는 값이 같은지 판단하지만 <br />assertSame은 객체의 레퍼런스가 같은지 판단한다.  ) |
|   assertNotSame(objA, objB)    |            객체 objA와 objB가 다른 객체인지 판단             |
|          assertfail()          |                   테스트를 바로 실패 처리                    |





## WebMvcTest (컨트롤러 단위 테스트)

```java
@WebMvcTest(DMakerController.class)
class DMakerControllerTest {
    @Autowired
    private MockMvc mockMvc;

    @MockBean // Mock 객체를 빈으로 등록
    private DMakerService dMakerService;

    // Media Type 지정
    protected MediaType contentType = new MediaType(MediaType.APPLICATION_JSON.getType()
            , MediaType.APPLICATION_JSON.getSubtype(),
            StandardCharsets.UTF_8
    );

    @Test
    void getAllDevelopers() throws Exception {
        DeveloperDto juniorDeveloperDto = DeveloperDto.builder()
                .developerSkillType(DeveloperSkillType.BACK_END)
                .developerLevel(DeveloperLevel.JUNIOR)
                .memberId("memberId")
                .build();
        DeveloperDto seniorDeveloperDto = DeveloperDto.builder()
                .developerSkillType(DeveloperSkillType.FRONT_END)
                .developerLevel(DeveloperLevel.SENIOR)
                .memberId("memberId2")
                .build();


        given(dMakerService.getAllEmployedDevelopers())
                .willReturn(Arrays.asList(juniorDeveloperDto, seniorDeveloperDto));
        mockMvc.perform(get("/developers").contentType(contentType))
                .andExpect(status().isOk())
                .andDo(print())
                .andExpect(
                        jsonPath("$.[0].developerSkillType",
                                  is(DeveloperSkillType.BACK_END.name()))
                ).andExpect(status().isOk())
                .andExpect(
                        jsonPath("$.[0].developerLevel",
                               is(DeveloperLevel.JUNIOR.name()))
                ).andExpect(status().isOk())
                .andExpect(
                        jsonPath("$.[1].developerSkillType",
                                is(DeveloperSkillType.FRONT_END.name()))
                ).andExpect(
                        jsonPath("$.[1].developerLevel",
                               is(DeveloperLevel.SENIOR.name()))
                );
    }
}
```



### WebMvcTest

- MVC를 위한 테스트

- 웹에서 Controller를 테스트 할 때 사용

- 요청과 응답에 대한 테스트를 할 수 있다.

- 시큐리티,  필터까지 자동으로 테스트할 수 있으며, 수동으로 추가/ 삭제가 가능하다.

- @SpringBootTest는 모든 빈을 로드하기 때문에 테스트 구동 시간이 오래 걸린다. 테스트 단위가 크기 때문에 디버깅이 어려울 수 도 있다. @WenMvcTest 어노테이션을 사용하면 Web Layer만 로드하기 때문에 테스트 구동 시간이 보다 빨라지고 가벼운 테스트가 가능하다.

- ex) @Controller, @ControllerAdvice, @JsonComponent, @Convert, @GenericConverter, Filter, WebMvcConfigurer, HandlerMethodArgumentResolver

- Security, Filter, Interceptor, request/response Handling, Controller의 항목들만 스캔하도록 제한하며, @Conponent는 스캔 대상에서 제외된다.

  

### mockMvc 메서드

- perform()

  - 요청을 전송하는 역할을 한다. 결과로 ResultActions 객체를 받으며, ResultActions 객체는 리턴 값을 검증하고 확인할 수 있는 andExcepct() 메서드를 제공한다.

- get("/url")

  - HTTP 메서드를 결정할 수 있다. (get(), post(), put(), delete())
  - 인자로는 경로를 보낸다.

- params(info)

  - 키=값의 파라미터를 전달할 수 있다.
  - 여러 개의 파라미터를 전달할 때는 params(), 하나일 때는 param()을 사용한다.

- andExcept()

  - 응답을 검증하는 역할을 한다.

  - 상태 코드(Status())

  - isOk() : 200

  - isNotFound() : 404

  - isMethodNotAllowed() : 405

  - isInternalServerError() : 500

  - is(int status) : status 상태 코드

  - 뷰 ( view() )

    - 리턴하는 뷰 이름을 검증한다.

    - ex ) view().name("developer") : 리턴 하는 뷰 이름이 developer인가?

  - 리다이렉트( redirect() )
    - 리다이렉트 응답을 검증한다.
    - ex) redirectUrl("/developer") : developer로 리다이렉트 되었는가?

  - 모델 정보( model() )
    - 컨트롤러에서 저장한 모델들의 정보 검증

  - 응답 정보 검증( content() )
    - 응답에 대한 정보를 검증해준다.

- andDo( print() )
  - 요청/응답 전체 메세지를 확인할 수 있다.



### ArgumentCaptor

```java
 ArgumentCaptor<T> name =  ArgumentCaptor.forClass(복사하려는 클래스)
```

- ArgumentCaptor을 사용하여 특정 메서드에 사용되는 아규먼트를 capture(저장)해 놓았다가 나중에 다시 사용(getValue) 할 수 있다.

### verify()

- verify()을 이용하면 mock 객체에 대한 원하는 메서드가 특정 조건으로 실행되었는지 검증 할 수 있다.



|                            메서드                            |                        기능                         |
| :----------------------------------------------------------: | :-------------------------------------------------: |
|                 verify(mock).method(param);                  |            해당 메서드를 호출했는지 검증            |
| verify(mock, times(wantedNumberOfInvocations)).method(param); |   해당 메서드가 정해진 횟수만큼 호출 됐는지 검증    |
| verify(mock, atLeast(minNumberOfInvocations)).method(param); | 해당 메서드가 최소 정해진 횟수만큼 호출 됐는지 검증 |
|          verify(mock, atLeastOnce()).method(param);          |      해당 메서드가 최소 한번 호출 됐는지 검증       |
| verify(mock, atMost(maxNumberOfInvocations)).method(param);  | 해당 메서드가 정해진 횟수보다 적게 호출됐는지 검증  |
|             verify(mock, never()).method(param);             |          해당 메서드가 호출 되었는지 검증           |



```java
  @Test
    void createDeveloperTest_success(){
        //given
        CreateDeveloper.Request request = CreateDeveloper.Request.builder()
                .developerLevel(DeveloperLevel.SENIOR)
                .developerSkillType(DeveloperSkillType.FRONT_END)
                .experienceYears(12)
                .memberId("memberId")
                .name("name")
                .age(32)
                .build();

        given(developerRepository.findByMemberId(anyString()))
                .willReturn(Optional.empty()); // return 값이 없으면 중복된 값이 없다는 것을 의미
        ArgumentCaptor<Developer> captor =
                ArgumentCaptor.forClass(Developer.class);
        //when
        CreateDeveloper.Response developer = dMakerService.createDeveloper(request);
        //then

        verify(developerRepository, times(1)).save(captor.capture());

        Developer savedDeveloper = captor.getValue();
        assertEquals(DeveloperLevel.SENIOR, savedDeveloper.getDeveloperLevel());
        assertEquals(DeveloperSkillType.FRONT_END, savedDeveloper.getDeveloperSkillType());
        assertEquals(12, savedDeveloper.getExperienceYears());

    }
```

