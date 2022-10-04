# Spring 초기 설정

### spring initializr 에서 설정

- Project : Gradle Project 

- Language : Java
- Depencies
  - Lombok
  - Spring Web
  - H2 데이터베이스를 사용할 경우 
    - H2 Database
    - H2를 사용할 경우 IntelliJ 에서의 application.properties를 application.yml 파일로 수정 후 아래 사진과 같이 값을 설정 

```yaml
spring:
  h2:
    console:
      enabled: true
```



- Validation
- Spring Data JPA



### IntelliJ 에서 환경설정

- Settings에 들어가서 아래 사진과 같이 설정한다

![image-20220816144054166](https://raw.githubusercontent.com/jaeseonNamgung/img_repo/main/img/image-20220816144054166.png)



- 포트 변경
  - Application 클래스를 마우스 오른쪽 버튼을 클릭 후 Modify Run Configuration을 클릭 그런 다음 아래 사진과 같이 포트 설정

![image-20220816144440432](https://raw.githubusercontent.com/jaeseonNamgung/img_repo/main/img/image-20220816144440432.png)