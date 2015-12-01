#스프링 프로젝트
##스프링 적용
###스프링을 어디에 적용할 것인가?
모든 자바 프로젝트에 사용할 수 있으나 탄생 배경은 **자바 엔터프라이즈** 환경을 위함
####자바 엔터프라이즈의 구조
**DB를 사용하는 웹 애플리케이션**    
클라이언트가 웹브라우저, 백엔드 시스템이 DB인 구성    
스프링이 사용되는 애플리케이션의 기본 구조    
스프링의 주요 기능이 집중되어 있다    
###어플리케이션 배포 서버
스피링 엔터프라이즈 어플리케이션을 배포하 위해서는 JavaEE 서버가 필요하면, 크게 **두 가지**로 구분    
####경량급 WAS/서블릿 컨테이너
웹 모듈의 배포만 가능    
EJB나 리소스 커넥터, WAS가 제공하는 분산 서비스 등이 지원되지 않음

선언적 트랜잭션, 선언적 보안, DB 연결 풀링 등 가능
####WAS
JavaEE의 대부분 표준 기술을 지원하고 다양한 형태의 모듈 배포가 가능    
고도의 안정성, 안정적인 리소스 관리, 레거시 시스템의 연동, EJB 모듈 사용 등이 가능    
상대적으로 관리 기능, 모니터링 기능이 뛰어나 여러 대의 서버를 동시에 운용할 때 유리    
###배포 단위
####독립 웹 모듈
WAR로 패키징한 독립 웹 모듈    
서블릿 컨테이너를 사용한다면 유일한 배포 방법    
가장 단순하고 편리한 배포 단위
####엔터프라이즈 애플리케이션
EAR(엔터프라이즈 애플리케이션)로 패키징    
EJB모듈 혹은 기타 웹 모듈과 통합해야 하는 경우에 사용
####백그라운드 서비스 모듈
RAR 패키징    
애플리케이션이 서버 내에서 백그라운드 서비스처럼 동작할 때 사용    
J2ee 1.4 이상의 표준을 따르는 WAS 필요    
##Maven
####전이적 의존 라이브러리 추적 기능
POM의 의존정보에 하나의 라이브러리를 지정하면, 지정된 라이브러리가 동작하는 데 필요한 여타 라이브러리까지 함께 다운로드    
####POM 파일 관리
필요한 스프링 모듈 혹은 라이브러리 등을 그루핑 하고 별도의 POM 파일로 관리    
목적에 맞게 POM 자체를 의존정보에 추가
```xml
<dependency>
	<groupId>...</groupId>
	<artifactId>...</artifactId>
	<version>...</version>
	<type>pom</pom>     //타입을 POM으로 하면 POM을 리포지토리에서 가져와 의존성을 추가
</dependency>
```
###스프링 모듈의 두 가지 이름과 리포지토리
동일한 파일에 대해 두 가지 명명규칙이 존재
####Maven 명명 규칙
**artifact id + version**    
`spring-core-3.0.7.RELEASE.jar`
```xml
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-core</artifactId>
	<version>3.0.7.RELEASE</version>
</dependency>
```
####OSGi 명명 규칙
`org.springframework.core-3.0.7.RELEASE.jar`
```xml
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>org.springframework.core</artifactId>
	<version>3.0.7.RELEASE</version>
</dependency>
```
Maven에서 사용하려면 별도의 리포지토리를 설정해야 한다