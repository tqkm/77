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
此时handlermapping的实现类是SimpleUrlHandlerMapping,它继承自AbstractUrlHandlerMapping，getHandler方法的主要实现就在该抽象类中，
