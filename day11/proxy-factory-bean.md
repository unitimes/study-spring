#스프링의 프록시
##ProxyFactoryBean
순수하게 프록시를 생성하는 작업만을 담당	
프록시에서 사용할 부가기능은 **MethodInterceptor** 인테페이스를 구현	
>###MethodInterceptor와 InvocationHandler
>InvocationHandler의 invoke() 메소드는 타깃 오브젝트에 대한 정보를 제공하지 않아 InvocationHandler의 구현체가 직접 타깃 오브젝트에 대한 정보를 알고 있어야 한다
>MethodInterceptor의 invoke메소드는 파라미터로 MethodInvocation을 받으며, MethodInvocation은 ProxyFactoryBean으로부터 타깃 오브젝트에 대한 정보를 받는다.
>클라이언트 입장에서 InvocationHandler 구현체의 instance를 생성할 때 직접 타깃 오브젝트를 넘겨줘야 하지만, ProxyFactoryBean을 사용하면 MethodInterceptor 구현체의 instance를 생성할 때 타깃 오브젝트를 넘겨줄 필요가 없다
>
>InvocationHandler의 invoke()메소드는 타깃의 정보를 파라미터로 받지 않기 때문에 타깃의 메소드를 실행하기 위해서 필드로 타깃 오브젝트 정보를 가져야 한다. 때문에 InvocationHandler의 구현체를 일단 빈으로 등록하고 나면 타깃 메소드 대상이 한정된다. 반면 MethodInvocation은 빈으로 등록되도 타깃의 정보를 갖지 않는다

기본적으로 ProxyFactoryBean은 타깃 오브젝트가 구현하고 있는 모든 인터페이스를 구현하는 프록시를 만든다. 따라서 ProxyFactoryBean이 구현할 인터페이스를 따로 지정할 필요 없다.		
그러나 타깃 오브젝트가 구현하고 있는 모든 인터페이스가 아니라 일부만 구현하고 싶다면 setInterfaces()를 통해 직접 지정이 가능		
###어드바이스, 포인트컷, 어드바이저
####개방폐쇄 원칙
다아나믹 프록시 생성 시, 메소드 선정 알고리즘(포인트 컷)과 부가기능(어드바이스)을 분리	
팩토리 빈에 포인트컷 등록 시 어드바이스를 한데 묶은 어드바이저로 등록
####interceptorNames
advice와 advisor를 list로 한번에 등록
#AOP
OOP를 돕는 기술	
핵심기술에서 부가기술을 분리하는 패러다임
```
스프링의 AOP는 프록시를 이용한다.
그러나 프록시를 이용하는 방법이 유일한 AOP 적용 방법은 아니다.

AspectJ의 경우 바이트코드를 직정 조작하는 방식으르 AOP를 적용한다.
```
####인터페이스와 프록시 AOP
>프록시 AOP가 반드시 interface를 필요로 하는 것은 아니다. Interface가 없는 class가 타깃이어도 프롯시 AOP는 정상적으로 동작
>
>다만 interface가 있을 때는 JDK proxy가 없을 때는 **CGLIB proxy**가 적용

##자동 프록시 생성
**ProxyFactoryBean을 사용할 때는**	
타깃이 다른 프록시를 만들 때마다 target 프로퍼티를 수정해야
###빈 후처리기
빈 오브젝트 생성 후 빈을 가공하는 방법을 제공	
####DefaultAdvisorAutoProxyCreator
Pointcut에 해당하는 모든 빈에 프록시를 자동으로 생성하고 컨테이너에 등록해주는 빈 후처리기

타깃 빈에 의존하는 다른 빈들은 프록시 오브젝트에 의존하도록 자동으로 바꿔 줌

등록된 빈 중에서 Advisor 빈(Advisor 구현체) 찾기도 자동
###포인트컷
```java
public interface Pointcut {
	ClassFilter getClassFilter();
	MethodMatcher getMethodMatcher();
}
```
메소드와 클래스 모두를 기준으로 프록시 타깃 설정 가능
##포인트컷 표현식
**AspectJExpressionPointcut** 클래스 사용	
빈으로 등록하여 사용
```java
<bean id="bean name" class="org.springframework.aop.aspectj.AspectJExpresstionPointcut">
	<property name="expression" value="execution(expression)" />
</bean>
```
###execution지시자 문법
메서드 실행에 대한 포인트컷
```
execution([접근제한자] 리턴타입 [패키지.클래스타입.]메서드이름 (파라미터 타입) [throws 예외이름])
```
- []는 생략 가능
- 접근제한자 생락은 조건을 부여하지 않겠다는 의미
- 리턴타입은 반드시 하나의 타입을 지정해야
	- *로 모든 타입 지정도 가능
- 패키지, 클래스 타입에 *사용 가능. '..'를 사용하여 모든 서브 패키지 선택 가능
	- 클래스 이름을 비교하는 것이 아니라 타입을 비교
	- A라는 클래스를 구현했거나 상속한 모든 클래스는 패키지와 클래스 이름에 상관없이 A 탑임에 해당당
- 메서드이름 *사용 가능
- 파라미터는 비워놓으면 파라미터가 없음을 지정하는 것
	- '..' 모두 다 허용
	- '...' 뒷부분 파라미터 조건 생략
 
##AOP 네임스페이스
###스프링 AOP 적용에 필요한 최소한의 빈
- 자동 프록시 생성기
- 어드바이스
- 포인트컷
- 어드바이저

###AOP 네임스페이스
xml환경설정에서는 AOP를 위한 빈들에 특화된(기능이 확장된) 네임스페이스를 제공
>`<aop:config>, <aop:pointcut>, <aop:advisor>` 세 가지 태그를 정의하면 어드바이스를 제외한 3가지 빈 자동 등록
##트랜잭션 속성
###트랜잭션 정의
####트랜잭션 전파
트랜잭션의 경계에서 이미 진행 중인 트랜잭션이 있을 때 또는 없을 때 어떻게 동작살 것인가를 결정하는 방식		
트랜잭션 시작 시 getTransaction()을 사용하는 이유

- PROPAGATION_REQUIRED
진행 중인 트랜잭션이 없으면 새로 시작, 이미 시작된 트랜잭션이 있으면 이에 참여
DefaultTransactionDefinition의 전파 속성
- PROPAGATION_REQUIRES_NEW
항상 새로운 트랜잭션 시작
- PROPAGATION_NOT_SUPPORTED
트랜잭션 없이 동작, 포인트컷의 복잡성을 낮추기 위해 사용

####격리수준
DB서버환경에서의 트랜잭션 격리수준. DefaultTransactionDefinition은 DataSource의 설정을 그대로 따르는 것으로 되어 있음
####제한시간
DefaultTransactionDefinition은 제한시간이 없는 것으로 되어 있음
####읽기전용
트랜잭션 내에서 데이터 조작 시도를 막음
#트랜잭션 인터셉터와 트랜잭션 속성
##TransactionInterceptor
트랜잭션 경계설정 어드바이스로 사용할 수 있도록 스프링에서 제공하는 어드바이스

PlatformTransactionManager와 Properties 타입의 transactionAttributes 두 개의 프로퍼티를 갖는다.
###예외처리 방식
기본적으로 런타임 예외일 경우만 롤백
###TransactionAttributes
메소드 패턴을 키(key)로 트랜잭션 속성의 컬렉션을 값(value)으로 하여 메소드 별로 트랜잭션 속성을 지정

**트랜잭션 속성**
>PROPAGATION_NAME, ISOLATION_NAME, readOnly, timeout_NNNN, -Exception1, +Exception2

- 전파 방식만 필수항목
- 제한시간은 초 단위
- -예외는 체크 예외 중에서 커밋 대상에서 제외(롤백 대상에 추가)
- +예외는 런타임 예외 중 커밋 대상에 예외 추가

###tx네임스페이스
xml환경설정 시 tx스키마를 사용하면 트랜잭션 속성을 개별 어트리뷰트를 통해 지정가능하여 환경설정 파일 가독성이 증가
>`<tx:advice>`를 정의하면 TransactionInterceptor 빈(Advice)이 자동 등록

##포인트컷과 트랜잭션 속성의 적용 전략
####트랜잭션 포인트컷은 클래스 단위가 바람직
- 메소드, 파라미너, 예외 정의하지 않는 것이 좋다
- 응집도가 높다면 트랜잭션 프록시의 타깃 클래스의 메소드는 모두 트랜잭션 적용 후보일 것
- 가능하면 클래스보다는 인터페이스 타입을 기준으로 적용
####최소한의 트랜잭션 어드바이스와 속성 정의
- 실제 하나의 애플리케이션에 사용할 트랜잭션 속성의 종류는 다양하지 않음
- 관리의 편이를 위해 메소드 명명 규칙을 만들 것
- 특별한 트랜잭션 속성이 필요한 경우엔 별도의 어드바이스와 포인트컷 사용

####같은 타깃 오브젝트 내의 메소드 호출에는 프록시 방식 AOP 적용 불가

##어노테이션 트랜잭션
어노테이션을 사용하면 타깃에 직접 트랜잭션 속성정보 지정 가능
###@Transactional
트랜잭션 속성정보로 사용되나, TransactionAttributeSourcePointcut에 의해 자동으로 포인트컷으로 등록	
클래스뿐 아니라 메서드마다 설정 가능
>그러나 메소드마다 @Transactional을 적용하면 동일한 속성 정보를 가진 어노테이션을 남발할 위험이 있다.

####@Transactional 적용 순서
구현체 메소드 > 구현체 클래스 > 인터페이스 메소드 > 인터페이스 클래스	
상속의 개념이 아니다. 구현체 메소드에 @Transactional을 적용하면 구현체 클래스에 적용한 @Transactional의 속성은 해당 메소드에 적용되지 않는다.
```
메소드 보단 클래스, 구현체 보단 인터페이스에 던저 적용하는 것이 바람직
```
 
#용어
####어드바이스(advice)
타깃 오브젝트에 적용하는 부가기능을 담은 오브젝트	
타깃 오브젝트에 종속되지 않는다
####포인트컷(Pointcut)
메소드 선정 알고리즘을 담은 오브젝트		
Pointcut 인터페이스 구현
####어드바이저(Advisor)
어드바이스와 포인트컷을 묶은 오브젝트
####애스펙트(Aspect)
애플리케이션의 핵심기능을 담고 있지는 않지만, 핵심기능에 부가되어 의미를 갖는 특별한 모듈	

어드바이스와 포인트컷을 함께 갖으며, 어드바이저는 단순한 형태의 애스펙트
####조인 포인트(join point)
어드바이스가 적용될 수 있는 위치	
스프링은 프록시를 통해 AOP를 구현하므로, 스프링 AOP의 조인 포인트는 타깃 오브젝트가 구현한 인터페이스의 모든 메소드이다.
#Etc.
####스태틱 멤버 클래스 빈으로 등록하기
**class 지정만 조금 상이**
class="package.ParentClass$InnerStaticClass"
####빈 설정의 상속
빈 설정할 때 parent 지정, 설정한 parent의 모든 설정을 가져 온다.	
오버라이드도 가능		
parent="bean name"