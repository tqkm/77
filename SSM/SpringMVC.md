SpringMVC的一次请求流程分析
---
Spring是通过DispatcherServlet分发请求的，通常在ssm框架中，会在web.xml中注册DispatcherServlet<br>
基于springboot2.0.4,主要的方法是DispatcherServlet中doDispatch方法，先看几个类中的成员变量<br>
~~~
@Nullable
private List<HandlerMapping> handlerMappings;//容器中注册的handlerMpapings

/** List of HandlerAdapters used by this servlet */
@Nullable
private List<HandlerAdapter> handlerAdapters;//容器中注册的handler适配器 
~~~
![image](https://github.com/wangda7/77/blob/master/picture/28.png)<br><br>
可以看到注册的handlerMappings与handlerAdapters，似乎比spring4.0版本多了一些。。。<br>
看第一个要分析的方法mappedHandler = getHandler(processedRequest)，其中mappedHandler是HandlerExecutionChain类，该类封装了当前的所有拦截器的引用（数组方式）和一个Object类的handler，至于handler究竟是什么，一步一步分析<br>
![image](https://github.com/wangda7/77/blob/master/picture/29.png)<br><br>
此时handlermapping的实现类是有很多，针对此次请求使用的是RequestMappingHandlerMapping，通过getHandlerInternal(request)方法获取handler，它继承自AbstractUrlHandlerMapping，getHandler方法的主要实现就在该抽象类中，进入方法，主要通过请求获取url路径，再根据URl路径和请求来获得HandlerMehod，而方法的返回值就是HandlerMethod，进入该类的构造方法<br><br>
![image](https://github.com/wangda7/77/blob/master/picture/30.png)<br><br>
其实就是要请求的类Controller和对应方法Method,此刻就知道Object类的handler是什么了
