#Annotation-based container configuration
환경설정 파일에 `<context:annotation-config/>` 적용	
Annotation injection이 XML injection보다 먼저 실행, 따라서 XML injection이 annotation injection을 override
##@Required
setter methods에 적용	
configuration time에 반드시 property가 설정되어야 함을 지정
##@Autowired
기본적으로 type을 기준으로 autowiring		
any methods, fields, constructors에 적용	
array 이나 typed collections에 적용하면 해당 type의 모든 빈이 wiring	
key type이 String인 경우 map type에도 적용 가능
>bean의 이름이 key가 됨

autowiring할 대상이 없으면 기본적으로 exception발생. 이를 막기 위해서는 required속성을 false로 해야 `@Autowired(required=false)`	
autowiring과정에서 대상이 없을 때에만 영향을 미침. required가 false여도 @Required이면 반드시 dependency가 configuration time에 설정되야(autowring이 아니어도)

Well-know resolvable dependencies는 @Autowired 주석만 달면 자동으로 결정됨
>ie. ApplicationContext

##@Primary
복수의 autowiring 후보가 있을 때, @Primay가 붙은 bean이 우선 됨
##@Qualifier("value")
autowiring 대상을 bean의 value로 특정	
bean의 value는 기본값이 bean name	
bean의 value는 고유값이 아님, 즉 여러 bean이 동일한 value를 가질 수 있음	
Typed collection에도 @Qualifier 사용 가능
>동일한 value를 가진 beans들이 autowired

**type이 아닌 name을 기반으로 injection을 할 목적이면 @Autowired 대신 @Resource를 쓸 것**
##@Resource
JSR-250 annotation이며 Spring에서 지원	
name 속성을 가지며, 스프링에 의해 기본으로 bean name이 설정		
name을 기반으로 autowiring 대상 탐색

해당하는 name이 없는 경우, @Autowired와 같이 type을 기반으로 매칭
##@PostConstruct
bean 생성 후 실행 될 메서드
##@PreDestroy
bean 파괴 전 실행 될 메서드
