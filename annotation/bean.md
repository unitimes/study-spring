#Spring Annotation 정리
##For bean
###@Component
Spring의 generic component	
@Repository, @Service, @Controller는 @Component의 특별 버전	
>|Annotation|Target bean|
>|----------|-----------|
>|@Repository|Persistence|
>|@Service|Service|
>|@Controller|Presentation|

@Repository, @Service, @Controller 대신 @Component를 써도 무방하나 **비추천**	
###Lite Bean
@Bean은 @Configuration 주석이 달리지 않은 class의 methods에도 사용 가능, 그러나 이 경우 'lite' mode로 처리가 되며, **다른 @Bean method를 호출할 수 없음**
###@Repository
DAO에 붙임	
Exception을 바꿔줌
>DAO과련 exception을 추상화 한 exception으로 translation

###@RestController
Composing @Controller and @ResponseBody
###Customizing
@ComponentScan의 Filter를 사용하면 component의 등록을 customizing 가능
