Spring
---
#### Spring的核心：IOC（控制反转）和AOP（面向切面）<br>
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
#### 循环依赖问题：<br>
spring有循环依赖问题，具体如下：<br>
创建一个A类，构造方法含有B的引用<br>
![image](https://github.com/wangda7/77/blob/master/picture/17.png)<br><br>
创建一个B类，构造方法含有C的引用<br>
![image](https://github.com/wangda7/77/blob/master/picture/18.png)<br><br>
创建一个C类，构造方法含有A的引用,这样，就形成了一个环<br>
![image](https://github.com/wangda7/77/blob/master/picture/19.png)<br><br>
选择使用构造方法注入时<br>
![image](https://github.com/wangda7/77/blob/master/picture/20.png)<br><br>
当启动时:<br>
![image](https://github.com/wangda7/77/blob/master/picture/21.png)<br><br>
#### 会报这个错误!!!<br>
![image](https://github.com/wangda7/77/blob/master/picture/22.png)<br><br>
原因在于spring创建bean时，首先会实例化该bean，然后为该bean注入属性，**对于正在创建的bean，会放在一个正在创建的bean池中，所以当发现要创建的bean在该池中时，会抛出org.springframework.beans.factory.BeanCurrentlyInCreationException异常**，所以，创建A时没问题，发现需要B，创建B，又发现需要C，创建C，发现需要A，可此时A正在创建，所以抛出异常。那么，spring如何解决这个构造器循环依赖问题呢？<br>
**三级缓存** 
~~~
/** Cache of singleton objects: bean name --> bean instance */
	private final Map<String, Object> singletonObjects = new ConcurrentHashMap<String, Object>(256);

	/** Cache of singleton factories: bean name --> ObjectFactory */
	private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<String, ObjectFactory<?>>(16);

	/** Cache of early singleton objects: bean name --> bean instance */
	private final Map<String, Object> earlySingletonObjects = new HashMap<String, Object>(16);
~~~
singletonObjects：单例对象缓存<br>
singletonFactories：单例对象工厂缓存<br>
earlySingletonObjects：提前曝光的单例对象缓存<br>
~~~
protected Object getSingleton(String beanName, boolean allowEarlyReference) {
		Object singletonObject = this.singletonObjects.get(beanName);
		if (singletonObject == null && isSingletonCurrentlyInCreation(beanName)) {
			synchronized (this.singletonObjects) {
				singletonObject = this.earlySingletonObjects.get(beanName);
				if (singletonObject == null && allowEarlyReference) {
					ObjectFactory<?> singletonFactory = this.singletonFactories.get(beanName);
					if (singletonFactory != null) {
						singletonObject = singletonFactory.getObject();
						this.earlySingletonObjects.put(beanName, singletonObject);
						this.singletonFactories.remove(beanName);
					}
				}
			}
		}
		return (singletonObject != NULL_OBJECT ? singletonObject : null);
	}
~~~
简单描述一下就是先从singletonObjects获取bean，如果没有，就从earlySingletonObjects获取bean，也没有的话，从singletonFactories获取bean,之后将其加入到earlySingletonObjects，同时删除singletonFactories中的bean,在这其中，关键的是singletonFactory对象<br>
~~~
protected void addSingletonFactory(String beanName, ObjectFactory<?> singletonFactory) {
    Assert.notNull(singletonFactory, "Singleton factory must not be null");
    synchronized (this.singletonObjects) {
        if (!this.singletonObjects.containsKey(beanName)) {
            this.singletonFactories.put(beanName, singletonFactory);
            this.earlySingletonObjects.remove(beanName);
            this.registeredSingletons.add(beanName);
        }
    }
}
~~~
这发生在createInstance后，即此时单例对象已经创建，但还有彻底完成，当创建A时，发现需要B，创建B，发现需要A，从提前曝光的singletonFactory中获取到A，初始化完成，然后A也初始化完成，这样便解决了循环依赖问题，即可以通过set/get注入的方式创建循环引用的对象，但构造器循环引用的是不能够解决的，因为singletonFactory获取到的对象是执行了构造器方法的
