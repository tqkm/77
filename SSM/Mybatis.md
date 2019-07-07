Mybatis
---
Mybatis是一款优秀的持久层框架，主要解决的是数据持久化和以及映射操作，底层是对jdbc操作的封装<br>
Mybatis大致的操作流程是通过XmlConfigBuilder读取配置文件构建Configuration对象，DefaultSqlSessionFactory创建SqlSession对象，通过SqlSession操作sql，
其中会通过jdk的动态代理生成接口的代理对象<br>
当spring与mybatis整合时:<br>
![image](https://github.com/wangda7/77/blob/master/picture/34.png)<br>
#### Mybatis的一些组件<br>
XmlConfigBuilder:主要是解析配置文件的<br>
Configuration：配置信息都存储这个类中，通过这个类的对象创建SqlsessionFactory对象<br>
SqlSessionFactory：创建SqlSession对象<br>
SqlSession：与数据库交互的一次会话<br>
ParameterHandler：处理sql参数<br>
StatementHandler：sql实行<br>
ResultsetHandler：结果返回集的处理<br>
Executor：操作数据库对象，一级缓存中的实现对象为SimpleExecutor，二级缓存中实现类为CachingExecutor<br>
MappedStatement:mapper.xml文件中对应的select、update、insert、delete节点对象<br>
BoudSql:sql语句就存储在这个对象中<br>
#### Mybatis缓存<br>
一级缓存:默认开启，作用范围为一次SqlSession会话，大致流程为根据要查询的sql生成一个key，根据key去hashmap中查询是否存在数据，不存在的话则去数据库查
询,同时将查询出的数据存储在hashmap中，判断sqlsession是否statement级别，是的话清除缓存<br>
一级缓存很容易产生脏数据，并且存储缓存的map没有大小限制，当执行了update、delete、insert缓存自动失效（查看源码知有这几个操作就会有删除缓存的操作）<br>
二级缓存:二级缓存的开启需要在配置文件和mapper.xml中分别操作.该级别的缓存是多个sqlsession共享，在同一个namespace下的操作语句，影响通过一个cache,但是当
多表联合查询时，该缓存就会失效，这是设计上的缺陷<br>
综上所述，mybatis的缓存最好就不要使用，会产生很多问题，可以通过其他三方介质实现缓存功能<br>
#### Mybatis插件<br>
~~~
public interface Interceptor {

  Object intercept(Invocation invocation) throws Throwable;

  Object plugin(Object target);

  void setProperties(Properties properties);

}
~~~
实现插件功能要在配置文件进行配置，同时实现类要实现该接口,通用mapper也是通过插件的方式实现的,主要是通过plugin方法用Plugin生成动态代理类，通过intercep
方法拦截要拦截的方法，比如分页查询，拦截到对应的方法后，取出sql语句，在sql语句后加上分页，覆盖原值<br>



