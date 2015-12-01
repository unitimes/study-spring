#스프링이란 무엇인가?
##스프링의 정의
```
자바 엔터프라이즈 개발을 편하게 해주는 오픈소스 경량급 애플레케이션 프레임워크
```
###애플리케이션 프레임워크
>####로즈 존슨
>"항상 프레임워크 기반으로 접근하라"

애플리케이션 전 영역을 관통하는 **일관된 프로그래밍 모델과 핵심 기술**을 각 분야의 특성에 맞게 적용

애플리케이션 **전 계층**과 **전 영역**에 전략과 기능을 제공해 애플리케이션을 편리하게 개발하게 해주는 애플리케이션 프레임워크

###경량급
불필요하게 무겁지 않음    
과거의 EJB와 대비되는 개념
>과거의 EJB는 매우 무겁고 복잡한 코드. 고가/고성능의 개발환경 요구

스프링은 서블릿 컨테이너만으로 EJB에서도 불편했던 고급 기능을 세련된 방식으로 적용할 수 있게 해줌    
프레임워크와 서버환경에 의존적인 부분을 제거하여 가벼운 비즈니스 코드 작성 가능

###자바 엔터프라이즈 개발을 편하게
편리한 애플리케이션 개발은 복잡한 로우레벨 기술에 신경 쓰지 않으면서 비즈니스 로직을 빠르고 효과적으로 구현 하는 것

스피링이라는 프레임워크가 제공하는 기술이 아니라 작성하는 애플리케이션 로직에 집중할 수 있도록

##스프링의 목적
###앤터프라이즈 개발의 복잡함
```
비즈니스 로직의 복잡함 + 기술적인 복잡함
```
복잡함을 줄이기 위해   
####_성격이 다른 두 가지 복잡함의 분리_가 첫번째

EJB 개발 방식이 비즈니스 로직에서 일부 기술적 코드를 제거해 준 것은 사실이나, **EJB라는 환경과 스펙에 종속**되는 비즈니스 코드를 만들어야하는 부담을 줌

EJB는 특정 틀을 강제했기 때문에, 상속/다형성 등 자바의 장점을 온전히 활용하기 어렵게 함

스프링은 기술의 적용이 비즈니스 코드에 직접 반영되지 않는 **비침투적인 기술**. 따라서 비즈니스 코드의 설계와 구현 방식을 제한하지 않음
>오히려 스프링에 종속된 코드가 비즈니스 코드에 등장하는 것을 지양

비즈니스 로직과 엔터프라이즈 기술 로직을 깔끔하게 분리

###스프링이 복잡함을 상대하는 전략
####기술적 복잡함 상대 전략
**특정 환경에 종속**    
같은 목적을 가진 코드가 서버, 라이브러리 등의 환경변화에 따라 지속적으로 변경 필요한 것이 이에 해당    
스프링은 서비스 추상화를 통해 이를 해결
>로우레벨의 기술 구현부와 기술을 사용하는 인터페이스를 분리하여 환경 변화에 독립적인 인터페이스를 제공

기술을 사용하는 클라이언트는 추상화된 인터페이스에 의존함으로써 핵심 로직에만 집중 가능

**기술과 비즈니스 로직의 혼재**    
스프링의 AOP는 기술과 비즈니스 로직의 분리를 가능하게 해줄 뿐만 아니라, 기술 코드의 중복 문제도 해결   

####비즈니스 로직의 복잡함 상대 전략
기술 이슈가 비즈니스 로직에 관여하는 것을 막아 객체지향을 실현할 수 있도록

####객체지향과 DI
```
DI는 좋은 오브젝트 설계의 결과물
```
객체지향에 충실한 설계가 가능하도록 단수한 오브젝트로 개발할 수 있고, 객체지향의 설계 기법을 잘 적용할 수 있는 구조를 만들기 위해 DI 같은 유용한 기술을 편하게 적용하도록 도와주는 것이 스프링의 기본 전략    
```
DI란 특별한 기술이라기보단 유연하게 확장할 수 있는 오브젝트 설계를 하다 보면 자연스럽게 적용하게 되는 객체지향 프로그래밍 기법
```
스프링은 비즈니스 로직 자체의 복잡함을 해결해주기 보단 DI 등을 통해 비즈니스 로직에만 집중할 수 있도록 해준다. 비즈니스 로직의 복잡함을 줄여주는 것은 **객체지향 설계**이다.

##POJO 프로그래밍
POJO: Plaing Old Java Object    
```
스프링의 정수는 엔터프라이즈 서비스(보안, 트랜잭션 등) 기능을 POJO에 제공하는 것
```
스프링의 주요 기술인 IoC/DI, AOP, PSA는 애플리케이션을 POJO로 개발할 수 있게 해주는 가능 기술    

POJO 프로그래밍: POJO에 애플리케이션의 핵심 로직과 기능을 담아 설계하고 개발하는 방법
###POJO의 조건
####특정 규약에 종속되지 않는다
특정 규약을 따르거나 반드시 특정 클래스를 상속해야 한다면 POJO가 아님    
####특정 환경에 종속되지 않는다
웹이라는 환경정보나 웹 기술을 담고 있는 클래스나 인터페이스 조차 사용해서는 안됨
####객체지향에 충실해야 한다
자바 문법을 지키고 JavaSE API만을 사용했다고 POJO인 것 아님
###POJO 프레임워크
POJO 프로그래밍이 가능하도록 기술적인 기반을 제공하는 프레임워크
>i.e. 스프링, 하이버네이트

##스프링의 기술
###IoC / DI
####DI의 활용
**의존하는 대상이 가진 핵심기능을 DI 설정을 통해 변경**    
가장 기본적인 활용 방법    
**핵심 기능을 동적으로 변경**    
프록시 오브젝트 기법    
**부가기능의 추가**    
데코레이터 패턴    
**인터페이스의 변경**    
어댑터 오브젝트(구현 오브젝트를 호출 해 상이한 인터페이스로 감싸주는 오브젝트)를 DI, 일관성 있는 추상화(PSA)를 가능하게 하는 것도 중간에 인터페이스 어댑터 역할을 해주는 레이어를 하나 추가한 것    
**프록시**    
지연된 로딩(lazy loading), 원격 프록시 등    
**싱글톤과 으브젝트 스코프**    
DI할 오브젝트의 생명주기(스코프) 제어    
**테스트**    
스텁 오브젝트, 목 오브젝트 사용    
###AOP
AOP는 OOP를 더욱 OOP답게 해준다    
####AOP 적용 기법
**다이내믹 프록시**    
데코레이터 패턴을 응용한 것, 자바의 객체지향 패턴을 활용한 방법이기 때문에 만들기 쉽고 간편. 부가기능 부여 포인트가 메소드의 호출이 일어나는 지점뿐이라는 제약    
**언어의 확장**    
AspectJ는 인스턴스 생성, 필드 액세스에도 조인 포인트를 제공. JDK만으로는 불가능 하며 사용하기 까다로움    
>스프링은 기본적으로 프록시 방식 사용하나 AspectJ를 사용하도록 설정 가능

####AOP의 적용
AOP는 하나의 모듈이 수많은 오브젝트에 보이지 않게 적용되기 때문에 주의해서 사용해야

###PSA
환경과 세부 기술의 변화에 관계없이 일관된 방식으로 기술에 접근할 수 있게 해주는 기술    
서비스 추상화를 위해 필요한 기술은 DI뿐