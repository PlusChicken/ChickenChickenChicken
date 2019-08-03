## Spring Mvc面试学习记录

### Spring Mvc的流程

1. 用户发送请求至`前端控制器`DispatcherServlet；
2. DispatcherServlet收到请求后，调用HandlerMapping`处理器映射器`，请求获取Handle；
3. 处理器映射器根据请求url找到具体的处理器，生成处理器对象以及处理器拦截器（如果有则生成），一并返回给DispatcherServlet；
4. DispatcherServlet调用HandlerAdapter`处理器适配器`；
5. HandlerAdapter通过适配调用`具体处理器`（Handler，也叫后端控制器）；
6. Handler执行完成返回ModelAndView；
7. HandlerAdapter将Handler执行结果ModelAndView返回给DispatcherServlet；
8. DispatcherServlet将ModelAndView传给View Resolver`视图解析器`进行解析；
9. ViewResolver解析后返回具体View；
10. DispatcherServlet对View进行渲染视图（即将模型数据填充至试图中）；
11. DispatcherServlet相应用户。

<hr>

### Spring Mvc用什么对象能够从后台向前台传递数据

使用ModelMap对象，在对象里调用put方法，把对象加到里面，前台就能够通过el表达式拿到。

<hr>

### 怎么把ModelMap中的数据放入Session中

可以在类的上面加上@SessionAttributes注解，里面的字符串就是要放入session里面的key

<hr>

### 如何解决post，get请求中文乱码问题

post请求是通过在web.xml中配置CharacterEncodingFilter过滤器，设置成为utf-8

get请求是对方法参数进行重新编码

<hr>

## HashMap和HashTable的区别

HashMap不是线程安全的，HashTable是线程安全的。

HashMap比HashTable效率高，因为HashTable是线程安全的。

HashMap允许空值和空键的，HashTable不允许为空。





 1、转发使用的是getRequestDispatcher()方法;重定向使用的是sendRedirect();

 2、转发：浏览器URL的地址栏不变。重定向：浏览器URL的地址栏改变；

  3、转发是服务器行为，重定向是客户端行为；

  4、转发是浏览器只做了一次访问请求。重定向是浏览器做了至少两次的访问请求；

  5、转发2次跳转之间传输的信息不会丢失，重定向2次跳转之间传输的信息会丢失（request范围）。

