#Day 08
##DB필드와 enum
DB의 raw value와 클라이언트에게 보여주어야 할 value의 type이 상이할 때가 존재
>사용자에게 레벨을 부여하는 서비스를 생각해보면,
>DB에는 사용자 레벨에 따라 특정 숫자를 부여하는 편이
>사용자에게는 숫자 보단 이해하기 쉬운 레벨을 보여주는 편이 좋다

이 때 상수형을 사용하는 것보다 enum을 사용하는 것이 좋다.	
프로그램 내부에서 돌아다니는 값은 숫자(정수)이기 때문에 레벨 범위 밖의 값이 입력되거나 의도하지 않게 변형이 될 위험이 높다.	
>enum을 사용할 때 db에 입력되야 하는 enum들은 숫자와 enum 간 변환하는 메서드를 구현할 것

##JdbcTemplate의 update()
몇개의 로우에 영향을 주었는지 확인하고 싶다면	
JdbcTemplate.update()의 input을 확인	
##Switch vs HashMap
성능의 차이는 고려 하지 않아도 될 정도	
다만, 유지 보수 차원에서 HashMap을 쓰는 것이 좋다	
>Command pattern 이라고도 한다

##Etc.	
- 메서드를 생성할 때는 특수한 맥락에서의 사용 뿐 아니라 독립적인 사용에 대해서도 반드시 고민해봐야 한다.
>특수한 맥랙에서 뿐만 아니라 독립적인 사용에서도 **문제가 발생하지 않도록** 작성해야 한다.

- 테스트도 구현부분과 의존성을 낮출 수 있게
