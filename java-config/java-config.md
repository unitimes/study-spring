#fromXMLtoJava
####Java configuration 파일에 XML configuration 파일 가져오기
@ImportResource("/applicationContext.xml")
####`<context:annotation-config />`의 처리
@Configuration 사용하면 `<context:annotation-config />` 불필요	
####Bean 참조
```java
@Bean
public DataSource dataSource() {
	...
}

@Bean
public PlatformTransactionManager transactionManager() {
	DataSourceTransactionManager tm = new DataSourceTransactionManager();
	tm.setDataSource(dataSource());    //bean 참조
	...
}
```
@Autowired를 사용 해 필드로 뺀 후 필드를 참조하거나,    
@Bean 메소드의 파라미터로 던져도 가능(가장 추천)
####Using @Transactional
@Transactional 어노테이션을 사용하기 위해서 

**XML** 에서는	
`<tx:annotation-driven />` 사용	

**Java configuration** 에서는		
Configuration class에 @EnableTransactionManagement를 추가	

**Spring boot** 에서는
@Transactional이 달린 메소드 혹은 클래스가 classpath에 존재하면 필요한 beans를 알아서 생성
####@SpringBootApplication annotation
@Configuration, @EnableAutoConfiguration, @ComponentScan 을 단 효과
####@EnableAutoConfiguration
classpath의 beans을 보고 필요한 beans를 설정 및 생성성

##Profiling configuration classes
테스트용 설정 파일과 운용을 위한 설정 파일을 분리해서 운영할 때	
Spring context에 사용할 configuration class를 @Profile로 특정 가능	
Activated profile은 Environment 오브젝트에 저장
####@Profile
메소드, 클래스(타입)레벨에 적용	
value값을 지정하면, 해당 value를 activation 하기 전까지 그냥 통과	
value에 array를 넣으면`@Profile({"p1", "p2"})` p1, p2 둘 중 하나 이상이 active 대상이 되면 처리	
!사용 가능 `@Profile({"p1", "!p2"})`
####Activation 방법
**Test인 경우**
@ActiveProfiles사용

**직접 Context를 생성 할 경우**
```java
AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext();
ctx.getEnvironment().setActiveProfiles("profile"...);
ctx.register(AppConfig.class...);
ctx.refresh();
```
>@ComponentScan이 달려있거나 모든 configuration classes를 @Import하고 있는 configuration class가 있다면 해당 class만 context에 register하면 됨

**property 사용**
properties 파일에
```
spring.profiles.active=dev,prod
```

##Properties
###Environment
컨테이너가 관리하는 환경 오브젝트	
프로퍼티가 저장 됨
####getProperty()
프로퍼티의 key를 매개변수로 value를 반환
###@PropertySource
property source를 Environment에 추가	
@Configuration과 함께 쓰임	
Type(class)에 적용
```java
@Configuration
@PropertySource("classpath:/com/cob/app.properties")
public class AppConfig {
	@Autowired
	Environment env;

	@Bean
	public TestBean testBean() {
		TestBean testBean = new TestBean();
		testBean.setName(env.getProperty("testbean.name"));
		return testBean;
	}
}
```
###Profile-specific properties
application.properties 외에 activated 된 profiles에 따라 profile-specific properties가 로드 됨	
`application-{profile}.properties`가 convention

Environment는 명시적으로 profiles를 activation 하지 않으면 default profiles을 갖고, 따라서 명시적으로 profiles를 activation 하지 않으면 `application-default.properties`가 로드 됨

###@Value
필드, 메소드, 파라메터에 적용	
expression을 사용해 필드에 di 할 때 사용	

**SPEL 사용**
`@Value("#{spel}")`
>_Default value_
>`@Value("#{spel ?: default_value}")`

**Property placeholder 사용**
`@Value("${property.key}")`
>_Default value_
>`@Value("${property.key : default_value}")`

####@PropertySource로 로드한 property를 사용하기 위해서
static으로 PropertySourcesPlaceholderConfigurer를 bean으로 등록해야 함
```java
@Bean
public static PropertySourcesPlaceholderConfigurer placeholderConfigurer() {
	return new PropertySourcesPlaceholderConfigurer();
}
```
Spring Boot를 사용한다면 자동으로 bean으로 등록되기 때문에 필요 없음

##Configuration Class의 재사용
Spring으로 독립적인 라이브러리를 만들어서 공급한다면, 설정파일을 java class로 만들어 라이브러리에 함께 패키징 함으로써, 클라이언트가 별도의 라이브러리 관련 설정 없이 라이브러리를 사용하게 할 수 있다.
>클라이언트는 자신의 스프링 프로젝트 설정파일에 라이브러리에서 제공하는 설정 java class를 import 해 주기만 하면 된다
>`@Import(LibConfig.class)`

###@Enable*
@Import의 대상을 특정한 어노테이션을 만들어 제공하면 클라이언트가 configuration class를 특정할 필요가 없어짐
>반드시 Enable로 시작해야 하는 것은 아님    
>@EnableTransactionManagement도 같은 방식

```java
@Import(LibConfig.class)
public @interface EnableLib {
}
```
###Autowiring과 Interface를 활용한 클라이언트와의 의존성 분리
라이브러리가 클라이언트의 특정 오브젝트 혹은 프러퍼티를 DI 받아야 하는 경우에도 라이브러리가 configuration class를 제공할 수 있다.	
만약 라이브러리가 클리이언트로부터 String name 프러퍼티를 받아야 한다면

**라이브러리의 configuration class**
```java
@Configuration
public class LibConfig {
	@Autowired
	NameDefiner nameDefiner;

	@Bean
	public SomeBean someBean() {
		SomeBean sb = new SomeBean();
		sb.setName(nameDefiner.getName());
		return sb;
	}
	...
}
```
**라이브러리의 NameDefiner 인터페이스**
```java
public interface NameDefiner {
	String getName();
}
```
**클라이언트의 configuration class - 별도의 NameDefiner 구현체 구현**
NameDefiner 구현체는 클라이언트에 있던 라이브러리에 같이 제공되든 상관없음
```java
@Configuration
@Import(LibConfig.class)
public class ClientConfig {
	@Bean
	public NameDefiner nameDefiner() {
		return new NameDefinerImpl("name");
	}
}
```
**클라이언트의 configuration class - NameDefiner 직접 구현**
@Configuration이 @Component를 메타 어노테이이션으로 가지고 있어서 가능
```java
@Configuration
@Import(LibConfig.class)
public class ClientConfig implements NameDefiner {
	@Override
	public String getName() {
		return "name";
	}
}
```
#Etc.
##등록된 빈 조회
대부분의 스프링 컨테이너는 DefaultListableBeanFactory 클래스로 빈을 등록하고 관리
```java
@Autowired
DefaultListableBeanFactory bf;

bf.getBeanDefinitionNames();	//등록된 빈들의 이름을 String[] 반환
```
##MyBatis와 Hibernate는 다른 프레임워크
MyBatis는 SQL mapper
Hibernate는 ORM