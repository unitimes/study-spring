#SQL과 DAO의 분리
##XML 설정 이용
####DAO 파일
```java
public class UserDaoJdbc implements UserDao {
	private Map<String, String> sqlMap;

	public void setSqlMap(Map<String, String> sqlMap) {
		this.sqlMap = sqlMap;
	}
	
	...
	
	public void add(User user) {
		this.jdbcTemplate.update(
			this.sqlMap.get("add"),
			user.getId(), user.getName());
	}
```
####XML
```xml
<bean id="userDao" class="package.UserDaoJdbc">
	<property name="dataSource" ref="dataSource" />
	<property name="sqlMap">
		<map>
			<entry key="add" value="insert into users(id, name) values(?,?)" />
			<entry key="getAll" vlaue="select * from users" />
		</map>
	</property>
</bean>
```
##XML설정과 SQL의 분리
DI설정 정보와 SQL이 섞여 있는 것은 바람직 하지 않다
>사실 이 문제는 XML 설정 파일을 여러 파일로 분리하면 해결

또한 XML이 아닌 다른 형태로 SQL을 공급할 수 있도록(**변화에 열린**)하는 것 중요

이를 위해 SQL를 제공해주는 인터페이스를 활용
####interface
```java
public interface SqlService {
	String getSql(String key) throws SqlRelatedNewException;
}
```
####DAO
```java
private SqlService sqlService;

...

public void add(User user) {
	this.jdbcTemplate.update(this.sqlService.getSql("userAdd"), user.getId(), user.getName());
}
```
>####생각 덧대기####
>key로 String을 활용하는 것보다 Enum을 활용하는 것이 실수를 줄이는 데 용이할 듯

##Resource 추상화
###Resource
스프링에서는 리소스 접근 방법을 통일할 수 있도록 Resource 인터페이스를 제공	
>리소스 접근 방법이란 리소스의 위치와 비슷한 맥락이다.
>classpath는 클래스패스 루트를 기준으로, file은 파일 시스템을 기준으로, http는 http 프로토콜로 접근

Spring은 리소스 접근 방법에 따라 다양한 리소스 구현체를 제공
>UrlResource, ClassPathResource, FileSystemResource 등

```java
Resource resourceFromClassPath = new ClassPathResource("com/cob/test/filename.ext");

Resource resourceFromFileSystem = new FileSystemResource("/document/filename.ext")
```

Resource 인터페이스는 InputStreamSource를 상속하고 있기 때문에, 모든 Resource 오브젝트는 **InputStream 형태**로 가져 올 수 있다.
###Resource 구현체의 선택과 ResourceLoader
하드 코딩으로 리소스 구현체를 특정하면 리소스 접근 방법 변화에 취약한 코드가 됨	
ResourceLoader 인터페이스의 getResource()와 prefix를 통해 di 가능

####XML 설정 이용
```xml
<bean id="beanId" class="...">
	<property name="resourceFromClassPath" value="classpath:com/cob/test/filename.ext" />
</bean>
```
####application.properties와 @Value 이용
**_application.properties_**
```
some.property=classpath:com/cob/test/filename.ext
```
**_java file_**
```java
@Value("${some.property}")
Resource resourceFromClassPath;
```
###ResourceLoader
Spring은 다양한 ResourceLoader 구현체를 제공	
그러나 getResource()의 String 파라메터가 prefixed 되어 있다면 모든 ResourceLoader 구현체는 동일한 역할을 함	
perfix가 붙어 있지 않을 때 default로 어떤 Resource 구현체를 사용할 것인가가 다름	

Spring의 application context는 ResourceLoader를 구현하고 있음
>@Autowired 등으로 context bean에 접근 해 getResource()메소드 사용 가능

#Etc.
##자기참조(self injection)
스프링은 자신을 참조하는 것이 가능
>Type 비교 방식으로 self-autowiring 불가
>@Resource를 사용해 name 비교 방식으로는 가능

