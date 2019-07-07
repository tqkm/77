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
![image](https://github.com/wangda7/77/blob/master/picture/31.png)<br><br>
图2
![image](https://github.com/wangda7/77/blob/master/picture/32.png)<br><br>
此时handlermapping的实现类是有很多，针对此次请求使用的是RequestMappingHandlerMapping，通过getHandlerInternal(request)方法获取handler，它继承自AbstractUrlHandlerMapping，getHandler方法的主要实现就在该抽象类中，进入方法，主要通过请求获取url路径，再根据URl路径和请求来获得HandlerMehod，而方法的返回值就是HandlerMethod，进入该类的构造方法<br><br>
![image](https://github.com/wangda7/77/blob/master/picture/30.png)<br><br>
其实就是要请求的类Controller和对应方法Method,此刻就知道Object类的handler是什么了<br>
看回图2，有一个getHandlerExecutionChain()方法，这个方法就是添加拦截器的<br>
![image](https://github.com/wangda7/77/blob/master/picture/33.png)<br><br>
有两个默认拦截器，至此，HandlerExecutionChain结束<br>
接下来是获取HandlerAdapter，确切的说是获取RequestMappingHandlerAdapter<br>
![image](https://github.com/wangda7/77/blob/master/picture/35.png)<br><br>
有三个注册的适配器，需要的是RequestMappingHandlerAdapter，后面有个ha.supports(handler)方法，主要是适配器是否支持该handler，判断的依据是是否支持方法的参数和返回值<br>
得到适配器后，之后执行拦截器的preHandle方法，其执行顺序是按照注册的顺序执行的，因为是按照数组的顺序从前往后执行<br>
![image](https://github.com/wangda7/77/blob/master/picture/36.png)<br><br>
调用适配器的handle方法，mv = ha.handle(processedRequest, response, mappedHandler.getHandler());也是在此方法中，真正使用反射去执行目标方法<br>
handle方法的实现是在AbstractHandlerMethodAdapter方法中，handle方法又调用抽象方法handleInternal(),调用的具体方法则RequestMappingHandlerAdapter的handleInternal()<br>
![image](https://github.com/wangda7/77/blob/master/picture/37.png)<br><br>
具体看RequestMappingHandlerAdapter的handleInternal()方法，在这里面用反射调用Controller中的方法<br>
![image](https://github.com/wangda7/77/blob/master/picture/38.png)<br><br>
能看到是调用mav = invokeHandlerMethod(request, response, handlerMethod)，返回一个ModelAndView对象，进去该方法,代码有很多，只找出了其中反射的
一部分，在此之前还会对参数做处理，暂不做分析<br>
![image](https://github.com/wangda7/77/blob/master/picture/39.png)<br><br>
会进入ServletInvocableHandlerMethod中的invokeAndHandle方法，该类继承了InvocableHandlerMehtod类，而InvocableHandlerMehtod类又继承了HandlerMehod，就是之前分析得到的handler<br>
进来便会调用invokeForRequest(webRequest, mavContainer, providedArgs)方法，获得方法的返回值。invokeForRequest()方法在InvocableHandlerMethod方法中已经实现<br>
![image](https://github.com/wangda7/77/blob/master/picture/40.png)<br><br>
首先获取方法的参数类型，之后调用doInvoke(args)方法，其中就是调用method.invoke()反射调用目标方法，获取返回值，之后在处理一下ModelAndView，返回到DispatcherServlet的doDispatch方法，之后就是执行拦截器的postHandle方法，注意执行的顺序是按照倒序执行，与之前的preHandle方法正好相反，之后就是处理ModelAndView对象，进行渲染了，返回客户端，整个流程基本结束


