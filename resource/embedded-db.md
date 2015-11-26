#Interface
##Interface의 상속이 필요한 경우
####새로운 클라이언트의 등장으로 기능이 확장 되어야 하는 경우
기존의 인터페이스에 기능 정의를 추가하는 것은 비추
>기존의 구현체도 변화해야 함
>
>_어차피 새로운 구현체는 필요하니 기존 클라이언트에 새로운 구현체를 DI하면 되지 않나?_
>기존 클라이언트에 사용하지 않는 기능을 노출할 필요가 있나? private을 사용하는 이유를 고려

구/신 클라이언트 모두 새로운 구현체에 의존한다고 하더라도 목적에 맞게 인터페이스를 달리 유지하는 것이 바람직

**인터페이스 분리 원칙**
#멀티스레드에서 동시성 문제
##멀티스레드에서 동시성 문제 없이 Map 사용
Collections.synchronizedMap()으로 동기화는 성능에 문제가 있을 수도 있음	
ConcurrentHashMap 사용 권장
##내장형 DB 사용
애플리케이션 메모리에 내장	

스프링이 지원하는 Derby, HSQL, H2 는 JDBC 드라이버를 제공하기 때문에 JDBC 프로그	래밍 모델을 그래로 사용하면 됨	
단 DataSource 인터페이스를 확장한 EmbeddedDatabase를 사용	
>EmbeddedDatabase는 shutdown() 기능을 DataSourcedp 추가
>내장형DB이기 때문에 애플리케이션 안에서 DB 종료 가능

###EmbeddedDatabase 사용
####EmbeddedDatabaseBuilder를 사용한 하드 코딩
```java
new EmbeddedDatabaseBuilder()
	.setType(EmbeddedDatabaseType.HSQL)
	.addScript("classpath:com/cob/schema.sql")
	...
	.build();
```
builder pattern를 사용한 초기화 과정이 필요, EmbeddedDatabase 생성 후 사용할 일 없음	
builder 자체를 빈으로 등록하는 것보다 builder를 사용하는 **팩토리 빈**을 만드는 것이 적합

jdbc 스키마를 사용하면 팩토리 빈을 자동 생성 가능
```xml
<jdbc:embedded-database id="embeddedDatabase" type="HSQL">
	<jdbc:script location="classpath:com/cob/shema.sql" />
</jdbc:embedded-database>
```
>스프링 컨테이너 종료시 자동으로 shutdown() 호출하도록 설정 됨

###트랜잭션과 SQL update
연관성이 높은 여러개의 SQL을 업데이트 해야 할 때 트랜잭션으로 묶으면 원자성이 보장	
Map 등의 Java collections 으로 SQL 관리 시는 원자성 보장 어려움	
>독립적이고(DB가 상이) 간결한 트랜잭션을 적용할 때는 TransactionManager를 사용하는 것보다 템플릿/콜백 패턴을 적용한 TransactionTemplate를 쓰는 것이 간편

#Etc.
##테스트 코드의 재사용
추상화 클래스와 상속을 통해 테스트 코드 재사용
```java
public abstract class AbstractTest {
	SomeInterfaceObjectProperty someProperty;

	@Before
	public void setUp() {
		someProperty = createSomeProperty();
	}

	abstract protected SomeInterfaceObjectProperty createSomeProperty();

	@Test
	...
}
```
서브클래스에서 추상클래스를 구현 해 구현 object를 변경해 동일한 테스트 진행