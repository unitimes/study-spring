#Day 06
## Spring boot with Spock
### Spring Context 사용하기
```
@ContextConfiguration(loader = SpringApplicationContextLoader.class, classes = [환경설정.class])
class TemplateCallbackSpec extends Specifications {
```
- @Autowired 등의 annotation 사용해 환경설정을 통해 생성된 Spring Context 사용 가능
### 파일 불러오기
```
getClass().getClassLoader().getResource("파일이름")
```
##Generics in Java
- If Foo is a subtype of Bar, and G is some generic type declaration, it is not the case that `G<Foo>` is a subtype of `G<Bar>`.

##Gradle 설정 파일
### Dependency
```
dependencies {
compile group: 'org.hibernate', name: 'hibernate-core', version: '3.6.7.Final'
}
```
```
dependencies {
compile 'org.hibernate:hibernate-core:3.6.7.Final'
}
```
- 위 둘은 동일한 dependency 설정
