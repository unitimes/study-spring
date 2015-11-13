#Day 09
##Transaction
###JDBC를 이용한 transaction 설정
```java
Connection c = dataSource.getConnection();

c.setAutoCommit(false);		//트랜잭션 시작
try {
	PreparedStatement st1 = c.prepareStatement("update ...");
	st1.executeUpdate();

	PreparedStatement st2 = c.prepareStatement("update ...");
	st2.executeUpdate();
	c.commit();
} catch(Exception e) {
	c.rollback();
}		//트랜잭션 끝

c.close();
```
JDBC는 DB 작업 수행 직후 자동으로 커밋이 되도록 기본설정 되어 있다.	
자동커밋 옵션을 false로 한 후 commit과 rollback을 직접 해 주어야 한다.
###트랜잭션 동기화(Transaction Synchronization)
JdbcTemplate는 update() 등의 메소드의 매 호출마다 connection을 생성하고 닫는다. 따라서 JdbcTemplate의 사용자가 임의로 트랜잭션을 만들 수 없다.	
트랜잭션 동기화를 이용하면 JdbcTemplate이 동기화된 하나의 connection을 참조하게 할 수 있다.	
스레드마다 독립적으로 트랜잭션 동기화 저장소를 갖기 때문에 thread safe	
스프링에서는 트랜잭션 동기화를 지원하는 유틸리티 메소드를 제공	
###스프링의 트랜잭션 서비스 추상화
Transaction 서비스에 Connection을 직접 사용하는 것은 JDBC, 하이버네이트는 session을 사용	
Connection을 사용하여 트랜잭션 설정하면, JDBC에 의존성이 생성되는 것	
개방폐쇄원칙을 위해선 트래잭션 서비스를 추상화 해야	
스프링에서는 트랜잭션 경계설정을 위한 추상 인터페이스 PlatformTransactionManager를 제공	
```
public void upgradeLevels() {
	PlatformTransactionManager transactionManager = 
			new DataSourceTransactionManager(dataSource);
	TransactionStatus status =
			transactionManager.getTransaction(new DefaultTransactionDefinition());     //트랜잭션 시작
	try {
		List<User> users = userDao.getAll();
		for (User user: users) {
			if (canUpgradeLevel(user)) {
				upgradeLevel(user);
			}
		}
		transactionManager.commit(status);
	} catch(RuntimeException e) {
		transactionManager.rollback(status);
		throw e;
	}                       //트랜잭션 종료
}
```
DI를 통해 TransactionManager 구현체를 받으면 트랜잭션 서비스의 DB에 대한 의존성을 제거 가능	
스프링에서 제공하는 모든 PlatformTransactionManager 구현체는 모두 싱글톤으로 사용 가능, thread safe	
###용어
- Transaction demarcation: 트랜잭션의 경계설정
	- Transaction의 시작과 끝을 지정하는 것
- Local transaction: 하나의 DB 커넥션 안에서 만들어지는 트랜잭션
- Global transaction: 복수의 DB 커넥션을 사용하는 트랜잭션

##단일 책임 원칙
Single Responsibility Principle, 하나의 모듈은 한 가지 책임을 가져야	
하나의 모듈이 바뀌는 이유는 책임에 변화가 있을 때만, 다른 모듈의 책임의 영향을 받아서는 안 됨.	
##외부 라이브러리와의 의존성 끊기
1. 클라이언트의 요구사항을 수행하는 interface를 만든다.
2. 외부 라이브러리를 사용하는 interface 구현체를 만든다.
3. DI를 통해 클라이언트에서 구현체를 사용한다.

###용어
- 서비스 추상화
>기능은 유사하나 사용 방법이 상이한 로우레벨의 다양한 기술에 일관성 있는 접근 방법을 제공해주기 위한 추상화


##Etc.
- 스프링 빈으로 등록할 때, 싱글톤 사용에 문제가 없는지 확인해야
	- thread safe 등
- Template method pattern을 사용하지 않고 상속을 통해 Polymorphism을 구현할 수는 없다. 그리고 template method pattern은 sub class의 super class에 대한 의존도가 너무 높다.
- interface + composition + di를 사용하면 중복없이, 의존도 낮게 polymorphism을 구현할 수 있다.