Spring
---
Spring的核心：IOC（控制反转）和AOP（面向切面）<br>
ioc:对象的创建交给spring容器来控制，对象创建的控制权由调用者变成了spring，可通过xml或基于注解的方式让spring创建对象<br>
set/get注入<br>
~~~
public class User {
    private int id;
    private String name;
    private int age;
}
<bean id="user" class="com.southwind.entity.User">
   <property name="id" value="1"></property>
   <property name="name" value="张三"></property>
   <property name="age" value="23"></property>
</bean>
~~~
构造器注入<br>
~~~
public Student(int id, String name, int age) {
        super();
        this.id = id;
        this.name = name;
        this.age = age;
}
<bean id="stu3" class="com.hzit.entity.Student">
   <constructor-arg name="id" value="3"></constructor-arg>
   <constructor-arg name="name" value="小明"></constructor-arg>
   <constructor-arg name="age" value="22"></constructor-arg>
</bean>
~~~
静态工厂<br>
~~~
public class StaticCarFactory {
    private static Map<Integer,Car> cars;
    static{
        cars = new HashMap<Integer,Car>();
        cars.put(1, new Car(1,"奥迪"));
    }
    public static Car getCar(int num){
        return cars.get(num);
    }
}
<bean id="car1" class="com.southwind.entity.StaticCarFactory" factory-method="getCar">
   <constructor-arg value="1"></constructor-arg>
</bean>
~~~
实例工厂<br>
~~~
public class InstanceCarFactory {
    private Map<Integer,Car> cars;
    public InstanceCarFactory() {
        cars = new HashMap<Integer,Car>();
        cars.put(2, new Car(2,"奥拓"));
    }
    public Car getCar(int num){
        return cars.get(num);
    }
}
<bean id="carFactory" class="com.southwind.entity.InstanceCarFactory"></bean>
<!-- 通过实例工厂对象创建 car 对象 -->
<bean id="car2" factory-bean="carFactory" factory-method="getCar">
    <constructor-arg value="2"></constructor-arg>
</bean> 

~~~
