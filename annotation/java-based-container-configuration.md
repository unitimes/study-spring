#Java-based container configuration
##@Configuration
Bean definitions source 임을 나타냄(설정파일)	
@Component로 meta-annotated 되어 있어 component-scan의 대상이 됨
###Inter-bean dependencies
```java
@Configuration
public class AppConfig {
	
	@Bean
	public Foo foo() {
		return new Foo(bar());
	}

	@Bean
	public Bar bar() {
		return new Bar();
	}
	
}
```
@Configuration 이 붙은 class의 @Bean 메소드가 아닌 경우(lite mode) 다른 @Bean 메소드 호출 불가	
@Bean 메소드가 여러번 호출 되도 같은 instance 반환
>CGLIB이 @Configuration class의 subclass를 만들고 parent method를 호출하기 전에 caching한 beans를 확인

##@Bean
Spring이 관리할 오브젝트 생성 메소드임을 나타냄	
메소드 이름이 기본적으로 bean name으로 설정	

initMethod, destroyMethod 속성을 지정하여 lifecycle callbacks 실행 가능
###name 속성
bean name 지정 가능	
복수의 이름 지정 가능
`@Bean(name = { "first", "second", "third" })`
###Lite @Bean mode
@Configuration이 달린 class 외의 class의 메소드에 @Bean이 달릴 때	
Lite mode의 @Bean 메소드는 다른 @Bean 메소드를 호출해서는 안 됨
>Inter-bean dependencies 선언이 어려움

##@Scope
Bean의 scope를 지정	
기본값은 singleton	

scope이 좁은 bean과 의존성을 갖게 하려면 proxy가 필요하며, proxyMode를 설정 해 proxy 생성 방법을 특정 할 수 있다. 기본 값은 ScopedProxyMode.NO이기 때문에 적절한 모드로 설정해 줘야 한다.
>**proxyMode**
>_ScopedProxyMode.TARGET_CLASS_
>CGLIB을 이용해 proxy 생성, CGLIB 필요, Spring 3.2 이후부터 spring-core에 포함
>_ScopedProxyMode.INTERFACES_
>JDK를 이용해 proxy 생성, interface 필요

##@Import
@Configuration class에 적용	
다른 @Configuration class의 @Bean definitions를 로딩할 때 사용	

스프링 context 인스턴스 생성 시 @Import에 지정한 @Configuration class는 지정 안해도 됨
>`@Import(FooConfig.class)`라면 FooConfig 클래스는 context 인스턴스 생성시 지정 안해도 됨

###다른 @Configuration class의 beans과 의존관계 설정
####@Bean 메소드의 파라미터로 던지기(가장 권장 됨)
```java
public class ServiceConfig {

    @Bean
    public TransferService transferService(AccountRepository accountRepository) {
        return new TransferServiceImpl(accountRepository);
    }

}

@Configuration
public class RepositoryConfig {

    @Bean
    public AccountRepository accountRepository(DataSource dataSource) {
        return new JdbcAccountRepository(dataSource);
    }

}

@Configuration
@Import({ServiceConfig.class, RepositoryConfig.class})
public class SystemTestConfig {

    @Bean
    public DataSource dataSource() {
        // return new DataSource
    }

}
```
####@Autowired, @Value 사용
@Configuration classes는 상당히 일찍 처리 된다. 따라서 @Autowired 등을 사용해 bean간에 의존관계를 설정할 경우 의도치 않게 일찍 초기화 되는 object가 발생할 수 있다.

beans를 @Autowired로 받을 수도 있고, @Configuration class 자체를 받을 수도 있으나 @Configuration class 자체를 받으면 @Configuration 간 결합도가 높아짐
>@Configuration class 자체를 @Autowired로 받고 싶다면 interface를 활용 해 구현체간 결합도 낮출 것 권장

```java
@Configuration
public class ServiceConfig {

    @Autowired
    private RepositoryConfig repositoryConfig;

    @Bean
    public TransferService transferService() {
        return new TransferServiceImpl(repositoryConfig.accountRepository());
    }
}

@Configuration
public interface RepositoryConfig {

    @Bean
    AccountRepository accountRepository();

}

@Configuration
public class DefaultRepositoryConfig implements RepositoryConfig {

    @Bean
    public AccountRepository accountRepository() {
        return new JdbcAccountRepository(...);
    }

}

@Configuration
@Import({ServiceConfig.class, DefaultRepositoryConfig.class}) // import the concrete config!
public class SystemTestConfig {

    @Bean
    public DataSource dataSource() {
        // return DataSource
    }

}
```