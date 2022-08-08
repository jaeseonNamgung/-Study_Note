스프링 5.x 버전을 사용하기 위한 설정

스프링 설정 순서:
스프링 버전 변경(3.1.1 -> 5.1.5이상) -> 스프링 관련 라이브러리(spring-tx, spring-jdbc, spring-test) ->
MyBatis 관련 라이브러리(HikariCP, MyBatis, mybatis-spring, Log4jdbc) -> Lombok(junit 4.7 -> 4.12변경) ->
servlet버전(2.5 -> 3.1.0[밑에 라이브러리 복사]) -> 자바 버전 변경(1.6 -> 1.8) ->
build Path에서 ojdbc6.jar 추가 ->maven-war-plugin라이브러리 추가-> RootConfig클래스 생성(DataSource,SqlSessionFactory)->
ServletConfig클래스(implements WebMvcConfigurer)->WebConfig클래스(
extends AbstractAnnotationConfigDispatcherServletInitializer)


1. 프레임 워크 버전 변경(3.1.1 -> 5.0.7)
<properties>
	<java-version>1.6</java-version>
	<org.springframework-version>5.1.5.RELEASE</org.springframework-version>
	<org.aspectj-version>1.6.10</org.aspectj-version>
	<org.slf4j-version>1.6.6</org.slf4j-version>
</properties>

2. java version 변경(1.6 -> 1.8)
<plugin>
	<groupId>org.apache.maven.plugins</groupId>
	<artifactId>maven-compiler-plugin</artifactId>
	<version>2.5.1</version>
	<configuration>
		<source>1.8</source>
		<target>1.8</target>
		<compilerArgument>-Xlint:all</compilerArgument>
		<showWarnings>true</showWarnings>
		<showDeprecation>true</showDeprecation>
	</configuration>
</plugin>

---------------------------------------------------------------------------------------------------------------------------
JavaConfiguration을 하는 경우
1. web.xml의 파일및 root-context.xml, servlet-context.xml 삭제

2. maven-war-plugin 추가
<plugin>
	<groupId>org.apache.maven.plugins</groupId>
	<artifactId>maven-war-plugin</artifactId>
	<version>3.2.0</version>
	<configuration>
		<failOnMissingWebXml>false</failOnMissingWebXml>
	</configuration>
</plugin>

3.@Configuration / RootConfig 클래스 생성

4. web.xml을 대신하는 클래스 작성
WebConfig 클래스를 생성한 후 extends를 사용해 AbstractAnnotationConfigDispatcherServletInitializer 상속 받고 
getRootConfigClasses() 메서드에 return new Class[] {RootConfig.class};를 작성한다.

5. ServletConfig.java를 생성

2가지 방법
- @EnableWebMvc 어노테이션과 WebMvcConfigurer 인터페이스를 구현하는 방식(과거에는
WebMvcConfigurerAdapter 추상 클래스를 사용했으나, 스프링 5.0버전부터는 Deprecated되었으므로 주의해야함)

- @Configuration과 WebMvcConfigurationSupport 클래스를 상속하는 방식 - 일반 @Configuration
우선 순위가 구분되지 않는 경우에 사용

ServletConfig 클래스에서 servlet-context.xml에 있는 코드를 작성해주고 
getServletConfigClasses() 메서드에서 return new class[] {ServletConfig.class}로 설정하고
WebConfig 클래스에 getServletMappings 메서드에서 return new String[] {"/"} 으로 설정

------------------------------------------------------------------------------------------------------------------------------------------
테스트 코드를 사용할 경우 (SpringJUnit4ClassRunner.class에 빨간 줄에 생길경우 spring 버전과 같은 버전으로 Spring TestContext Framework 라이브러리를 추가해야한다.
<!-- https://mvnrepository.com/artifact/org.springframework/spring-test -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>${org.springframework-version}</version>
    <scope>test</scope>
</dependency>

---------------------------------------------------------------------------------------------------------------------------------------------
lombok 라이브러리 설치
-> 원하는 경로에 설치후 cmd에서 설치 경로에 java -jar lombok.jar 입력후 다운

junit을 4.7 -> 4.12로 변경

lombok 라이브러리 설정
<!-- https://mvnrepository.com/artifact/org.projectlombok/lombok -->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.16</version>
    <scope>provided</scope>
</dependency>
-------------------------
서블릿 버전이 2.5버전을 사용하지만 Java 설정 등을 이용하려면 3.0이상을 사용하는게 좋기 때문에 pom.xml에 
기존에 2.5를 삭제하거나 주석처리 하고 3.1.0버전 이상을 설정한다.

<!-- Servlet -->
<!-- <dependency>주석처리 또는 삭제
	<groupId>javax.servlet</groupId>
	<artifactId>servlet-api</artifactId>
	<version>2.5</version>
	<scope>provided</scope>
</dependency> --> 

- 3.1.0 servlet 버전 추가
<!-- https://mvnrepository.com/artifact/javax.servlet/javax.servlet-api -->
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>3.1.0</version>
    <scope>provided</scope>
</dependency>

-----------------------------------------------------------------------------------------------------------------------------------------------
데이터베이스 연동
1. 커넥션 풀 설정 
<dependency>
      	<groupId>com.zaxxer</groupId>
   	<artifactId>HikariCP</artifactId>
  	<version>4.0.3</version>
</dependency>
-----------------------------------------------------------------------------------------------------------------------------------------------
스프링 관련 라이브러리
<!-- https://mvnrepository.com/artifact/org.springframework/spring-test -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>${org.springframework-version}</version>
    <scope>test</scope>
</dependency>
<!-- https://mvnrepository.com/artifact/org.springframework/spring-jdbc -->
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-jdbc</artifactId>
	<version>${org.springframework-version}</version>
</dependency>

<!-- https://mvnrepository.com/artifact/org.springframework/spring-tx -->
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-tx</artifactId>
	<version>${org.springframework-version}</version>
</dependency>
-------------------------------------------------------------------------------------------------------------------------------------------------
MyBatis 관련 라이브러리 추가
-spring-jdbc/spring-tx: 스프링에서 데이터베이스 처리와 트랜잭션 처리(해당 라이브러리들은 
MyBatis와 무관하게 보이지만 추가하지 않은 경우에 에러가 발생하므로 주의해야한다.)

- mybatis/mybatis-spring: MyBatis와 스프링 연동용 라이브러리

mybatis(3.4.6)-mybatis-spring(1.3.2)-spring-tx(${org.springframework-version})-
spring-jdbc(${org.springframework-version})
<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis-spring -->
<dependency>
	<groupId>org.mybatis</groupId>
	<artifactId>mybatis-spring</artifactId>
	<version>1.3.2</version>
</dependency>

<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
<dependency>
	<groupId>org.mybatis</groupId>
	<artifactId>mybatis</artifactId>
	<version>3.4.6</version>
</dependency>
<dependency>
    <groupId>org.bgee.log4jdbc-log4j2</groupId>
    <artifactId>log4jdbc-log4j2-jdbc4</artifactId>
    <version>1.16</version>
</dependency>
<dependency>
      	<groupId>com.zaxxer</groupId>
   	<artifactId>HikariCP</artifactId>
  	<version>4.0.3</version>
</dependency>

-------------------------------------------------------------------------------------------------------------------------------------------------
log4jdbc-log4j2설정
1. 라이브러리 추가
<!-- https://mvnrepository.com/artifact/org.bgee.log4jdbc-log4j2/log4jdbc-log4j2-jdbc4 -->
<dependency>
    <groupId>org.bgee.log4jdbc-log4j2</groupId>
    <artifactId>log4jdbc-log4j2-jdbc4</artifactId>
    <version>1.16</version>
</dependency>
2. properties 설정
log4jdbc.spylogdelegator.name=net.sf.log4jdbc.log.slf4j.Slf4jSpyLogDelegator

3. rootContext.xml에서 driverClassName에 value 값을 변경
net.sf.log4jdbc.sql.jdbcapi.DriverSpy

4. rootContext.xml에서 jdbcUrl에 value 값을 변경
jdbc:log4jdbc:oracle:thin:@localhost:1521:XE

-------------------------------------------------------------------------------------------------------------------------------------------------
테스트 로그양을 줄이게 하기위한 설정
- src/test/resources안에 log4j.xml에서 코드를 추가
<logger name="jdbc.audit">
	<level value="warn"/>
</logger>
<logger name="jdbc.resultset">
	<level value="warn"/>
</logger>
<logger name="jdbc.connection">
	<level value="warn"/>
</logger>

-------------------------------------------------------------------------------------------------------------------------------------------------
log4j.xml에서 빨간줄이 생길경우
<!DOCTYPE log4j:configuration SYSTEM
   "http://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/xml/doc-files/log4j.dtd">

@log4j가 빨간줄이 생길경우
pom.xml에서 <scope>runtime</scope>를 주석처리 한다. 
(</exclusions> 밑에 있는거를 주석처리해야한다.)

-------------------------------------------------------------------------------------------------------------------------------------------------
Controller의 메서드 리턴 타입을 VO(ValueObject)나 DTO(Data Transfer Object)타입 등 복합적인 데이터가 들어간 객체 타입으로 지정할 경우
주고 JSON 데이터를 만들어 내는 용도로 사용하기 때문에 JSON 데이터를 사용할려면 라이브러리를 설정해야 한다.
<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind -->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.4</version>
</dependency>
-------------------------------------------------------------------------------------------------------------------------------------------------
파일 업로드 설정
<!-- https://mvnrepository.com/artifact/commons-fileupload/commons-fileupload -->
<dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.3.3</version>
</dependency>

