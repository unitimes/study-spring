#Day 10
##단위 테스트와 통합 테스트
항상 단위 테스트가 적합한 것은 아니다.	
>DAO는 로직이 들어 있는 코드가 아니라 DB 접근이 목적인 코드. 따라서 DB를 연동해 테스트하는 것이 가치 있다.

여러 개의 단위가 의존관계를 가지고 동작할 때를 위한 통합 테스트는 필요	
단위 테스트를 만들기가 너무 복잡하다면, 처음부터 통합 테스트를 고려해본다. 그러나 통합 테스트에 참여하는 단위 중에서 가능한한 많은 단위를 단위 테스트하는 것이 좋다	
스프링 설정 자체도 테스트 대상이 될 수 있다. 또한 스프링을 이용해 보다 추상적인 레벨에서 테스트를 해야 하는 경우도 있다. 이럴 경우 스프링 테스트 컨텍스트 프레임워크를 이용하라	
테스트는 코드가 작성되고 빠르게 진행되야 한다. TDD는 코드를 만들자마자 바로 테스트가 가능	
테스트하기 편한 코드는 깔끔하고 좋은 코드가 될 가능성이 높다.
> 결합도가 높은 코드는 테스트하기 어려운 반면, 결합도가 낮은 코드는 테스트하기 쉽다

테스트 코드를 잘 작성해 놓으면 리팩토링이 쉬워진다.	
###Mock
####Mockito 프레임워크
특정 호출에 특정 리턴
```java
when(mockUserDao.getAll()).thenReturn(this.users);
```
특정 메소드의 호출 횟수 확인
```java
verify(mockUserDao, times(2)).update(any(User.class));
```
파라미터 캡처
```java
ArgumentCaptor<SimpleMailMessage> mailMessageArg = ArgumentCaptor.forClass(SimpleMailMessage.class);
verify(mockMailSender, times(2)).send(mailMessageArg.capture();
List<SimpleMailMessage> mailMessages = mailMessageArg.getAllValues();
```
##프록시 패턴과 데코레이터 패턴
동일한 인터페이스를 구현하는 프록시와 타깃을 사용	
핵심기능과 부가기능으로 나누어 부가기능은 프록시가, 핵심기능은 타깃이 구현	
클라이언트가 프록시 참조, 프록시가 타깃 참조하는 것이 기본 구조
###용어
- 프록시( proxy): 대리인. 실제 오브젝트인 것처럼 위장하여, 클라이언트의 요청을 대신 받음
- 타깃(target)/실체(real subject): 프록시를 통해 최종적으로 요청을 위임받아 처리하는 실제 오브젝트

### 데코레이터 패턴
타깃에 부가적인 기능을 런타임 시 다이내믹하게 부여해주기 위해 프록시를 사용하는 패턴	
부가적인 기능에 따라 여러개의 프록시 사용 가능	
> 순서대로 프록시가 다른 프록시를 참조
> 마지막 프록시가 타깃을 참조

```java
InputStream is = new BufferedInputStream(new FileInputStream("a.txt"));
```
- InputStream 인터페이스 구현한 타깃인 FileInputStream에 버퍼 읽기 기능을 제공하는 데코레이터 패턴
###프록시 패턴
프록시라는 용어와 구분 필요	
프록시를 사용하는 방법 중에서 타깃에 대한 접근 방법을 제어하려는 목적을 가진 패턴	
타깃의 기능을 확장하거나 추가하지 않음
####프록시 패턴이 필요한 경우
- 당장 필요하지 않지만 타깃 오브젝트에 대한 레퍼런스가 미리 필요한 경우
- 원격 오브젝트를 이용하는 경우
- 본래 수정 가능한 오브젝트에 대해, 특정 레이어에서는 읽기전용으로 동작하게 강제해야 하는 경우
### 다니내믹 프록시
####프록시 구성의 단점
- 타깃의 인터페이스를 구현하고 위임하는 코드 작성이 번거로움
- 부가기능 코드의 중복
####리플렉션
자바의 코드 자체를 추상화해서 접근하도록 만든 것
```java
Method lengthMethod = String.class.getMethod("length");
```
- "length"라는 이름을 갖고, 파라미터는 없는 스트링의 메소드를 가져오는 코드
- Method 인터페이스의 invoke() 메소드를 사용 해 실행 가능
- Method.invoke()는 모든 예외를 InvocationTargetException으로 한 번 포장
	- getTargetException()으로 본래 예외 가져올 수 있음.

####다이나믹 프록시 생성
리플렉션을 사용하여 프록시를 동적으로 생성	
코드에서 프록시 팩토리에 프록시 생성을 요청`Proxy.newProxyInstance()`하면 런타임에 동적으로 생성
```java
Hello proxyHello = (Hello)Proxy.newProxyInstance(
	getClass().getClassLoader(),    //다이나믹 프록시 클래스 로딩에 사용할 로더
	new Class[] { Hello.class },    //구현할 인터페이스
	new UppercaseHandler(new HelloTarget()));    //부가기능과 위임코드를 담은 InvocationHandler
```
####InvocationHandler
구현해야 할 메서드는 invoke() 하나	
다이나믹 프록시의 모든 인터페이스 호출이 invoke()를 거치게 됨	

```java
public class UppercaseHandler implements InvocationHandler {
	Object target;

	public UppercaseHandler(Object target) {
		this.target = target;
	}

	public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
		/*
		인터페이스의 모든 메서드 호출에 적용되기 때문에
		Method의 이름(getName()), return type 등을 활용하여
		메서드를 구분해, 부가기능 구현 및 타깃이로 위임
		*/
	}
}
```
####다이나빅 프록시 오브젝트 빈으로 등록하기
스프링의 빈은 기본적으로 클래스 이름과 프러퍼티를 가지고 리플렉션을 이용해 생성	
다이나믹 프록시 오브젝트는 구현 클래스 이름을 가지고 있지 않아 기본적 방식으론 빈 생성 불가	
팩토리 빈을 사용하여 생성해야	
#####*팩토린 빈(Factory bean)*
스피링을 대신해 오브젝트의 생성로직을 담당하도록 만들어진 빈
>스프링은 리플렉션을 활용해 빈을 생성하기 때문에 private 생성자만을 가진 클래스도 기본적인 방법으로 빈 생성이 가능, 그러나 그렇게 되면 생성자를 private으로 만든 특별한 이유를 무시하게 되는 것

FactoryBean 인터페이스를 구현한 클래스를 스프링의 빈으로 등록하면 팩토리 빈으로 동작	그러나 등록되는 빈은 FactoryBean이 아니라 FactoryBean이 생성하는 오브젝트
>구현한 FactoryBean의 getObject() 메서드의 return 값에 해당


##Etc.
###Stub vs. Mock	
- Stub: 미리 정해 놓은 결과물을 뱉는다. 기대되는 호출 없이 호출이 있으면 정해 놓은 리턴
- Mock: 특정 기대에 따라 미리 프로그램된 객체. 기대되는 호출과 그에 상응하는 리턴

###Groovy에서 array를 파라미터로 넣는 방법
- groovy는 default로 List를 사용
- new String[] { "java", "c", "javascript" } -> [ "java", "c", "javascript" ] as String[]

###@Component에서 application.properties 사용하기
*in java file*
```java
@Component
public class MessageFactoryBean implements FactoryBean<Message> {
	@Value("${message.text}")
	private String text;
	
	...
}
```
*in application.properties*
```
message.text=Text text
```
