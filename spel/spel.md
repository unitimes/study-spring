#Spring Expresstion Language(SpEL)
##in XML or Annotations
###Properties 접근
`systemProperties['property.key']`
systemProperties는 자동으로 정의되어 있음
###bean 접근
bean name로 접근 가능
```XML
<bean id="numberGuess" class="org.spring.samples.NumberGuess">
    <property name="randomNumber" value="{ T(java.lang.Math).random() * 100.0 }"/>

    <!-- other properties -->
</bean>

<bean id="shapeGuess" class="org.spring.samples.ShapeGuess">
    <property name="initialShapeSeed" value="{ numberGuess.randomNumber }"/>

    <!-- other properties -->
</bean>
```