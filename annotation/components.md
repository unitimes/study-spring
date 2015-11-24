#Components
##@ComponentScan
```java
@Configuration
@ComponentScan(basePackages = "org.example")
public class AppConfig {
	...
}
```
component autodetection을 위한 annotation	
basePackages는 콤마, 세비콜론, 빈공백 등을 통해 복수 지정 가능. value와 alias 맺어져 있기 때문에 `@ComponentScan("org.example")`와 동일	

XML 환경 설정에서 `<context:component-scan base-package="org.example">`에 해당
>`<context:component-scan>`을 사용하면 `<context:annotation-config>` 자동으로 사용 됨

autodetection 대상을 includeFilters, excludeFilters 속성을 설정해 특정 가능
##@Component
Spring의 generic component	
@Repository, @Service, @Controller는 @Component의 특별 버전	
>|Annotation|Target bean|
>|----------|-----------|
>|@Repository|Persistence|
>|@Service|Service|
>|@Controller|Presentation|

@Repository, @Service, @Controller 대신 @Component를 써도 무방하나 **비추천**	
##Lite Bean
@Bean은 @Configuration 주석이 달리지 않은 class의 methods에도 사용 가능, 그러나 이 경우 'lite' mode로 처리가 되며, **다른 Bean에서 @Bean method를 호출할 수 없음**
##@Repository
DAO에 붙임	
Exception을 바꿔줌
>DAO과련 exception을 추상화 한 exception으로 translation