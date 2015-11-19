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


#용어
####어드바이스(advice)
타깃 오브젝트에 적용하는 부가기능을 담은 오브젝트	
타깃 오브젝트에 종속되지 않는다
