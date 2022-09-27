# Spring系列学习

## **简要概括SSM**

1）Spring 是一个集成了控制反转（IoC）和面向切面（AOP）设计思想的容器框架。它可以装载 Bean，也就是 Java 中的类，包括 Service、Dao 、Controller层里面的，并且能够管理对象的生命周期和配置，比如对象的创建、销毁、回调等；利用控制反转这个机制，我们就不用在每次使用类的时候用 new 关键字声明并初始化。另外，Spring 事务管理也是开发中常用到的。

2）来看一下 SpringMVC 的工作原理：

- 客户端发送请求到 DispacherServlet（分发器）
- 由 DispacherServlet 控制器查询 HandlerMapping，找到处理请求的 Controller
- Controller 调用业务逻辑处理后，返回 ModelAndView
- DispacherSerclet 查询视图解析器，找到 ModelAndView 指定的视图
- 视图负责将结果显示到客户端

3）MyBatis 是一个支持普通 SQL 查询，存储过程和高级映射的持久层框架。它对 JDBC 做了封装，让数据库底层操作变的更透明了。 cMyBatis 的操作都是围绕着一个叫 sqlSessionFactory 的实例展开的，通过配置文件关联到各个实体类的 Mapper 文件，再由 Mapper 文件映射每个类对数据库所需要执行的 SQL 语句。



## Spring的简单笔记

### IOC简介

​	IOC主要解决的是对象管理和对象依赖的问题。当一个对象创建时，它所依赖的对象由外部传递给它，而非自己去创建所依赖的对象（比如通过new操作）。因此，也可以说在对象如何获取它的依赖对象这件事情上，控制权反转了。这便不难理解控制反转和依赖注入这两个名字的由来了。

而这个外部就是IOC容器。可以理解为一个对象工厂，我们把对象都交给这个工厂，工厂管理这些对象的创建以及依赖关系的创建。

主要好处在于将**对象集中统一管理**，并且**降低类之间的耦合度**。**而Spring框架很好的实现了IOC的特性，会自然而然得促进你应用一些好的设计原则，会帮助你开发出更加“高内聚低耦合”的软件。**



参考：Spring IoC有什么好处呢？ - David的回答 - 知乎 https://www.zhihu.com/question/23277575/answer/121015545





### IoC实现原理

简述：工厂模式（Bean Factory）+反射机制与XML解析技术（获取XML定义的类文件路径等格式，使用反射动态生成对象）

包扫描获取类绝对路径与类相关信息-》类实例化

我们可以举一反三，像mybaits、Hibernate的配置文件，我们大概就可以知道是如何解析的了。比如mybatis的配置文件，里面的sql配置，如何去拼接sql,无非就是解析文档，把标签里面的内容拿出来，拼接成String，交给jdbc去运行等等诸如此类。

<u>IoC主要的实现方式有两种：依赖查找，依赖注入。</u>

**说明DI是IOC的一种实现方式。**



**手写SpringMVC**

关于注解实现bean的实例化和依赖注入，以及SpringMVC的方法映射简单实现：

1. 声明注解
2. 编写实现类，根据运行时获取类和方法的信息以及注解
3. 根据注解信息，如Autowired或Controller或RequestMapping，实现相关操作
4. 根据请求的路径，截取信息找到映射，执行相关方法

https://www.bilibili.com/video/av31786799?from=search&seid=6489083373551445869

怎么阅读Spring源码？ - 王奕然的回答 - 知乎 https://www.zhihu.com/question/21346206/answer/101789659



### Spring中的BeanDefinition（核心概念）

![image-20220211234344500](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/image-20220211234344500.png)



### 理解Spring中的bean对象

与普通构造方法生成的对象区别：由Spring容器代理，对属性进行依赖注入等初始化行为生成的对象。并放入在框架中的Map<beanName, Bean对象>中。（单例模式map）

![image-20211213225748030](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/202206161045928.png)



### Spring Bean的生命周期

当Factory调用getBean方法时，Bean开始被创建：

![image-20211213225953964](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/202206161047161.png)

Spring IOC 容器对 bean 的生命周期进行管理的过程： 

① 通过构造器或工厂方法创建 bean 实例（**实例化**） 

② 为 bean 的属性设置值和对其他 bean 的引用（依赖注入）

*③ 将 bean 实例传递给 bean 后置处理器的postProcessBeforeInitialization()方法* 

④ 调用 bean 的初始化方法 

*⑤ 将 bean 实例传递给 bean 后置处理器的 postProcessAfterInitialization()方法*

⑥ bean 可以使用了

⑦当容器关闭时调用 bean 的销毁方法



**注：bean 的后置处理器（BeanPostProcessor） **

*① bean 后置处理器允许在调用初始化方法前后对 bean 进行额外的处理*

*② bean 后置处理器对 IOC 容器里的所有 bean 实例逐一处理，而非单一实例。其典型应用是：检查 bean 属性的正确性或根据特定的标准更改bean 的属性。*

*③ bean 后置处理器时需要实现接口： org.springframework.beans.factory.config.BeanPostProcessor。在初始化方法被调用前后，Spring 将把每个 bean 实例分别传递给上述接口的以下两个方法：●postProcessBeforeInitialization(Object, String)   ●postProcessAfterInitialization(Object, String)*

![image-20220227190047523](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/image-20220227190047523.png)





### AOP

还没读的：https://www.cnblogs.com/xuxinstyle/p/13531799.html

在我们的程序中，经常存在一些系统性的需求，比如权限校验、日志记录、统计等，这些代码会散落穿插在各个业务逻辑中，非常冗余且不利于维护。



我的理解是像这种系统性的需求，如何实现，有以下几个步骤：

1. 首先定义一个切面（创建一个类使用@Aspect和@Component注解标上，来表示这是一个切面类并且能被Spring托管）

2. 接着在这个切面（类）里设置切入点（Pointcut）和实现通知（Advice）。切入点代表哪些类的方法会被处理。通知即切面中定义的一个方法，被切点包含的方法执行过程中都会被这个通知处理！

3. 关于通知，需要了解这个AspectJ包。AspectJ是Java 社区里最完整最流行的 AOP 框架。常用的通知注解都是属于它支持的，如基本的5种类型：

   ① **@Before**：前置通知，在方法执行之前执行 

   ② **@After**：后置通知，在方法执行之后执行 

   ③ @AfterRunning：返回通知，在方法返回结果之后执行 

   ④ @AfterThrowing：异常通知，在方法抛出异常之后执行 

   ⑥ **@Around**：环绕通知，围绕着方法执行。这个功能最强大，具体看示例注释。

   一个大致的切面内容如下示例：

   ```java
   package com.nowcoder.community.aspect;
   
   import org.aspectj.lang.ProceedingJoinPoint;
   import org.aspectj.lang.annotation.*;
   import org.springframework.stereotype.Component;
   
   @Component
   @Aspect
   public class AlphaAspect {
   
       //下面的这种写法叫重用切入点定义，实际上切入点表达式可以直接写在通知注解里。其他通知可以通过方法名称引入该切入点，起到重用的效果。
       //每个*的含义:1.返回值;2.类;3.方法;    (..):任意参数
       @Pointcut("execution(* com.nowcoder.community.service.*.*(..))")
       public void pointcut() {
   
       }
   
       @Before("pointcut()")
       public void before(JoinPoint joinPoint) {
           System.out.println("before");
       }
   
       @After("pointcut()")
       public void after() {
           System.out.println("after");
       }
   
       @AfterReturning("pointcut()")
       public void afterRetuning() {
           System.out.println("afterRetuning");
       }
   
       @AfterThrowing("pointcut()")
       public void afterThrowing() {
           System.out.println("afterThrowing");
       }
   
       @Around("pointcut()")
       public Object around(ProceedingJoinPoint joinPoint) throws Throwable {
           System.out.println("around before");
           //1.在增强处理方法体内，调用ProceedingJoinPoint的proceed方法才会执行目标方法：这就是@Around增强处理可以完全控制目标方法执行时机、如何执行的关键！如果程序没有调用ProceedingJoinPoint的proceed方法，则目标方法不会执行。
           //2.调用ProceedingJoinPoint的proceed方法时，还可以传入一个Object[]对象，该数组中的值将被传入目标方法作为实参——这就是Around增强处理方法可以改变目标方法参数值的关键。这就是如果传入的Object[]数组长度与目标方法所需要的参数个数不相等，或者Object[]数组元素与目标方法所需参数的类型不匹配，程序就会出现异常。
           Object obj = joinPoint.proceed();
           System.out.println("around after");
           //环绕通知的方法需要返回目标方法执行之后的结果，即调用 joinPoint.proceed(); 的返回值，否则会出现空指针异常
           return obj;
           总结：@Around注释的通知可以实现控制被截取的方法的执行时间和如何执行！可以修改传入的参数等，起到改变方法执行效果的作用。
       }
   
   }
   
   ```

   

4. 然后需要了解在通知的形参中可以定义`JointPoint` 对象。这个对象很有用，可以用它来获取一个签名，利用签名可以获取请求的包名、方法名，包括参数（通过 `joinPoint.getArgs()` 获取）等。具体实现如下：

   ```java
   // 获取签名
   Signature signature = joinPoint.getSignature();
   // 获取切入的包名
   String declaringTypeName = signature.getDeclaringTypeName();
   // 获取即将执行的方法名
   String funcName = signature.getName();
   
   // 也可以用来记录一些信息，比如获取请求的 URL 和 IP
   ServletRequestAttributes attributes = (ServletRequestAttributes)RequestContextHolder.getRequestAttributes();
   HttpServletRequest request = attributes.getRequest();
   
   // 获取请求 URL
   String url = request.getRequestURL().toString();
   
   // 获取请求 IP
   String ip = request.getRemoteAddr();
   log.info("用户请求的url为：{}，ip地址为：{}", url, ip);
   ```

   

5. 以上基本就是大致的步骤，AOP不过如此。最后再看两张图，系统的理解整个AOP结构。

   ![在这里插入图片描述](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/20201016194259600.png)

   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201016201545227.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L211X3dpbmQ=,size_16,color_FFFFFF,t_70#pic_left)



以上部分内容感谢外链：https://blog.csdn.net/mu_wind/article/details/102758005

 

#### 拦截器过滤器AOP

拓展内容（Spring 过滤器 拦截器 AOP区别通俗易懂）：https://blog.csdn.net/dreamwbt/article/details/82658842

**三者使用场景**

三者功能类似，但各有优势，从过滤器--》拦截器--》切面，拦截规则越来越细致，执行顺序依次是过滤器、拦截器、切面。

- 过滤器并没有定义业务逻辑执行前、后等，仅仅是请求到达就执行。
- 拦截器有三个方法，相对于过滤器更加细致，有被拦截逻辑执行前、后等。
- 面向切面拦截的是类的元数据（包、类、方法名、参数等）相对于拦截器更加细致，而且非常灵活，拦截器只能针对URL做拦截，而AOP针对具体的代码，能够实现更加复杂的业务逻辑。

![image-20211222140845434](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/image-20211222140845434.png)

还有尚硅谷SpringPDF第四章和第五章。

完。



### Spring注解学习

@ComponentScan

@Conditional

@Import

@Configuration

参考链接：https://segmentfault.com/a/1190000022694615

尚硅谷课程：https://www.bilibili.com/video/BV1gW411W7wy

相应笔记：https://liayun.blog.csdn.net/article/details/115053350



#### @Configuration与@Bean注解的原理

@Configuration与@Bean结合使用。@Configuration可理解为用spring的时候xml里面的<beans>标签，@Bean可理解为用spring的时候xml里面的<bean>标签。

参考：https://blog.csdn.net/sundacheng1989/article/details/92783515



#### **@Component和@Bean的区别是什么？**

- @Component注解表明一个类会作为组件类，并告知Spring要为这个类创建bean。
- @Bean注解告诉Spring这个方法将会返回一个对象，这个对象要注册为Spring应用上下文中的bean。通常方法体中包含了最终产生bean实例的逻辑。

**对比：**

相同点：**两者的结果都是为spring容器注册Bean.**

不同点：@Component 通常是通过类路径扫描来自动侦测以及自动装配到Spring容器中。

@Bean 注解通常是我们在标有该注解的方法中定义产生这个bean的逻辑。



#### 通过注解装配bean

##### **组件装配**

Controller 组件中往往需要用到 Service 组件的实例，Service 组件中往往需要用到 

Repository 组件的实例。Spring 可以通过注解的方式帮我们实现属性的装配。



**@Autowired 注解和@Resource注解**

@Autowired 

- ①先根据类型byType寻找匹配的bean，如果有多个匹配则再使用byName实现自动装配。 
- ②构造器、普通字段(即使是非 public)、一切具有参数的方法都可以应用@Autowired注解

```java
@Autowired
@Qualifier("testServiceImpl2")
private ITestService testService;
```



`@Qualifier("XXX")`中的 XX是 Bean 的名称（格式是类名开头小写），所以 @Autowired 和 @Qualifier 结合使用时，自动注入的策略就从 byType 转变成 byName 了



@Resource的作用相当于@Autowired，只不过@Autowired按byType自动注入，而@Resource默认按 byName自动注入罢了。

如果通过接口引用类型实现装配并且该接口有多个实现类，则需把名字写对（默认格式是类名开头小写），否则就要用name属性指定了。

```java
@Resource(name = "testServiceImpl")
private ITestService testService;
```

参考：https://www.cnblogs.com/happyyang/articles/3553687.html



学习链接：[**Spring必须掌握的内容**](https://baijiahao.baidu.com/s?id=1679863781346563082&wfr=spider&for=pc)





## Spring MVC相关内容

**MVC与三层架构的对应关系，图示如下：**

![img](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/20190603151709124.png)





### [访问controller中的方法和访问网页的跳转和重定向](https://blog.csdn.net/qq_41902066/article/details/108527854?utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-2.control&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-2.control)



### SpringMvc中Model、ModelAndView理解与使用

model一般作为控制器中方法的参数传入，方法返回String类型配合视图解析器拼接视图路径。

ModelAndView即封装model与视图路径的功能。实例需要我们手动new，方法返回ModelAndView类型；setViewName设置视图路径。

参考链接：https://www.pianshen.com/article/6188336285/

### 常用请求参数注解

#### 控制器方法形参常用注解

![2](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/2-1629271247738.JPG)





#### [@Controller和@RestController的区别？](https://www.cnblogs.com/shuaifing/p/8119664.html)

知识点：**@RestController注解相当于就相当于为类添加了@Controller注解，并且为其中的每个方法添加了@ResponseBody注解。**

1) 如果只是使用@RestController注解Controller类，则Controller类中的方法无法返回jsp页面，或者html，配置的视图解析器 InternalResourceViewResolver不起作用，返回的内容就是Return 里的内容。

2)**@RestController注解，相当于@Controller+@ResponseBody两个注解的结合，返回json数据不需要在方法前面加@ResponseBody注解了。但使用这个注解，就不能返回jsp,html页面，视图解析器无法解析jsp,html页面**



#### `@RequestBody`与`@ResponseBody`

以普遍使用的 json 数据为例，**这两个注解的作用分别可以将请求中的数据解析成 json 并绑定为实体对象以及将响应结果以 json 格式返回给请求发起者**。

但 Http 请求和响应是基于文本的，也就是说在 SpringMVC 内部维护了一套转换机制**HttpMessageConverter**，也就是我们通常所说的“**将 json 格式的http请求信息转换为一个对象，将对象转换为 json 格式并输出为响应信息** ”，这些就是 HttpMessageConverter 的作用。

- @RequestBody可以获取请求体，需要在控制器方法设置一个形参，使用@RequestBody进行标识，当前请求的请求体就会为当前注解所标识的形参赋值或对象装配

- @ResponseBody可以将该方法的返回值直接作为响应报文的响应体响应到浏览器。Spring Boot 会直接将对象转换为 json 格式并输出为响应信息

@requestBody详解参考：https://blog.csdn.net/justry_deng/article/details/80972817/



另外，HttpMessageConverter还提供了一个注解**@RequestEntity**



<u>添加 `@ResponseBody` 注解后，Spring Boot 会直接将对象转换为 json 格式并输出为响应信息</u>，这是将对象作为相应数据的例子。 而使用 `@RequestBody` 则代表api接收前端请求并将参数转换为后端定义的对象

拓展：https://blog.csdn.net/justry_deng/article/details/80972817/



#### [@RequestMapping与的简化形式：GetMapping和@PostMapping等新注释](https://www.jdon.com/springboot/requestmap.html)

@RequestMapping注解的作用就是将请求和处理请求的控制器方法关联起来，建立映射关系。



主要作用：又称组合注解，来帮助简化常用的HTTP方法的映射，并更好的表达被注解方法的语义。如原注解：

@RequestMapping(value = "/get/{id}", method = RequestMethod.GET)

新注解方法可以简化为：

@GetMapping("/get/{id}")

拓展：

```java
//该方法用于处理 "/", "/index", "index.html" 等请求，这种路径的请求一般为首页请求
@GetMapping({"/", "/index", "index.html"})
```



#### IntelliJ Idea解决Could not autowire. No beans of 'xxxx' type found的错误提示

方法1：在mapper文件上加@Repository注解，这是从spring2.0新增的一个注解，用于简化 Spring 的开发，实现数据访问 

方法2：在mapper文件上加@Component注解，把普通pojo实例化到spring容器中，相当于配置文件中的<bean id="" class="">



### spring MVC工作流程

![image-20211029095255978](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/image-20211029095255978.png)

1) 用户向服务器发送请求，请求被SpringMVC 前端控制器 DispatcherServlet捕获。

2) DispatcherServlet对请求URL进行解析，得到请求资源标识符（URI），判断请求URI对应的映射：

如果不存在：

i. 再判断是否配置了mvc:default-servlet-handler

ii. 如果没配置，则控制台报映射查找不到，客户端展示404错误

iii. 如果有配置，则访问目标资源（一般为静态资源，如：JS,CSS,HTML），找不到客户端也会展示404错误

3) 根据该URI，调用HandlerMapping获得该Handler配置的所有相关的对象（包括Handler对象以及

Handler对象对应的拦截器），最后以HandlerExecutionChain执行链对象的形式返回。

4) DispatcherServlet 根据获得的Handler，选择一个合适的HandlerAdapter。

5) 如果成功获得HandlerAdapter，此时将开始执行拦截器的**preHandler**(…)方法【正向】

6) 提取Request中的模型数据，填充Handler入参，开始执行Handler（Controller)方法，处理请求。

在填充Handler的入参过程中，根据你的配置，Spring将帮你做一些额外的工作：

- a) **HttpMessageConveter**： 将请求消息（如Json、xml等数据）转换成一个对象，将对象转换为指定
- 的响应信息
- b) 数据转换：对请求消息进行数据转换。如String转换成Integer、Double等
- c) 数据格式化：对请求消息进行数据格式化。 如将字符串转换成格式化数字或格式化日期等
- d) 数据验证： 验证数据的有效性（长度、格式等），验证结果存储到BindingResult或Error中

7) Handler执行完成后，向DispatcherServlet 返回一个ModelAndView对象。

8) 此时将开始执行拦截器的**postHandle**(...)方法【逆向】。

9) 根据返回的ModelAndView（此时会判断是否存在异常：如果存在异常，则执行

HandlerExceptionResolver进行异常处理）选择一个适合的ViewResolver进行视图解析，根据Model

和View，来渲染视图。

10) 渲染视图完毕执行拦截器的afterCompletion(…)方法【逆向】。

11) 将渲染结果返回给客户端。



### 重定向和转发的区别

1、请求次数

重定向是浏览器向服务器发送一个请求并收到响应后再次向一个新地址发出请求，转发是服务器收到请求后为了完成响应跳转到一个新的地址；重定向至少请求两次，转发请求一次；

2、地址栏不同

重定向地址栏会发生变化，转发地址栏不会发生变化；

3、是否共享数据

重定向两次请求不共享数据，转发一次请求共享数据（在request级别使用信息共享，使用重定向必然出错）；

4、跳转限制

重定向可以跳转到任意URL，转发只能跳转本站点资源；



### HTTP状态码

HTTP状态码（HTTP Status Code）是用以表示网页服务器HTTP响应状态的3位数字代码。它由 RFC 2616 规范定义的，并得到RFC 2518、RFC 2817、RFC 2295、RFC 2774、RFC 4918等规范扩展。

表示页面请求的状态值，分别有：

200表示请求成功

303表示重定向

400表示请求错误

401表示未授权

403表示禁止访问

404表示文件未找到

500表示服务器错误



### SpringBoot中的SpringMVC

在`spring-boot-starter-web.pom`文件中，描述里这样写道：

```xml
<description>Starter for building web, including RESTful, applications using Spring
		MVC. Uses Tomcat as the default embedded container</description>
```

本Starter使用 Spring MVC 构建 Web 应用程序，包括 RESTful 应用程序。使用 Tomcat 作为默认的嵌入式容器。



#### 如何在springboot自定义配置springmvc

`Spring Boot 2.x` 中，`自定义SpringMVC` 配置可以通过实现 `WebMvcConfigurer` 接口来完成。





## Springboot的持久层框架

### JPA规范

JPA， 全称Java Persistence API，翻译为JAVA持久层应用接口，**JPA本身并不是一个框架，它本质上是一种ORM规范**，如Hibernate是一个框架，也是JPA的一种实现。

![image.png](https://segmentfault.com/img/bVbDq2x)

参考：https://segmentfault.com/a/1190000021755149



### JPA和Mybatis

​	像**Spring Data JPA是全自动框架**，不需要自己写sql，当然也可以自己写sql实现。而自动生成sql这点是优点，也是缺点，因为生成的sql可读性差，而且一些业务比如执行逻辑删除等还是需要自己来实现sql。 　　

​	**Mybatis是一种半自动的ORM框架**，它简单易上手，没有第三方依赖，支持对象与数据库的ORM关系映射，**将sql代码与业务代码分离**，使得开发人员可以更自如的写出高效的sql，不过反过来说不像SpringData JPA这种全自动的ORM框架，它需要自己实现sql语句，对开发人员的sql编写能力要求高，虽然将sql代码写在xml文件里方便了修改和编写，可这同时也降低了可读性。

参考：https://developer.aliyun.com/article/822764



## Mybatis

### resultMap自定义映射

自定义 resultMap，实现高级结果集映射。如一对多的映射关系



### 动态SQL

**if**

使用 <if> 来判断条件是否为空，是否拼接SQL

```sql
<if test="colum!=null"></if>
```



**if+where**

<where>主要解决<if>中的每个条件判断之间可能会存在以and 为开头返回导致的错误SQL

```sql
<select id="selectUserByUsernameAndSex" resultType="user" parameterType="com.ys.po.User">
    select * from user where
        <if test="username != null">
           username=#{username}
        </if>
         
        <if test="username != null">
           and sex=#{sex}
        </if>
</select>

<--usename为空时，那么查询语句为 select * from user where and sex=#{sex}，这是错误的SQL-->

<select id="selectUserByUsernameAndSex" resultType="user" parameterType="com.ys.po.User">
    select * from user
    <where>
        <if test="username != null">
           username=#{username}
        </if>
         
        <if test="username != null">
           and sex=#{sex}
        </if>
    </where>
</select>
```

这个“where”标签会知道如果它包含的标签中有返回值的话，它就插入一个‘where’。此外，如果标签返回的内容是以AND 或OR 开头的，则它会剔除掉。



**set**

作用于更新操作的set关键字的字段拼接



**trim**

trim标记是一个格式化的标记，可以完成set或者是where标记的功能。如：

- prefix: 添加前缀 
- prefixOverrides: 去掉前缀 
- suffix: 添加后缀 

- suffixOverrides: 去掉后缀

相关参考: https://www.cnblogs.com/ysocean/p/7289529.html#_label0





### 常见问题

#### 1、#{}和${}的区别是什么？

答：**简单说#{}是预编译处理，${}是字符串替换。**

- `${}`是 Properties 文件中的变量占位符，它可以用于标签属性值和 sql 内部，属于静态文本替换，比如${driver}会被静态替换为`com.mysql.jdbc. Driver`。**Mybatis 在处理${}时，就是把${}替换成变量的值。**
- `#{}`是 sql 的参数占位符，MyBatis 会将 sql 中的`#{}`替换为? 号，在 sql 执行前会使用 PreparedStatement 的参数设置方法，按序给 sql 的? 号占位符设置参数值，比如 ps.setInt(0, parameterValue)，`#{item.name}` 的取值方式为使用反射从参数对象中获取 item 对象的 name 属性值，相当于 `param.getItem().getName()`。
- #{}可以有效预防SQL注入，提高系统安全性。



拓展：SQL注入

1. ' or 1=1#法

   ```sql
   #根据我们提交的用户名和密码被合成到SQL查询语句当中之后是这样的
   select * from users where username='test' and password=md5('test')
   
   #当在用户名输入框中输入:' or 1=1#,密码随便输入，这时候的合成后的SQL查询语句为：
   select * from users where username='' or 1=1#' and password=md5('') 
   #可以看到在sql语法中''以外的#字符被视作注释符号，这样后面的就不会去执行了
   #等价于
   select* from users where usrername='' or 1=1
   ```

2. admin'#法



结尾：**为什么PreparedStatement能防止sql注入呢？**

​	因为sql语句是预编译的，而且语句中使用了占位符，规定了sql语句的结构。用户可以设置"?"的值，但是不能改变sql语句的结构，因此想在sql语句后面加上如“or 1=1#”实现sql注入是行不通的。

```java
//演示代码
Class.forName(com.mysql.jdbc.Driver);       //加载驱动
Connection con = DriverManager.getConnection("jdbc:mysql://....");  //创建与mysql中某个数据库的连接

String sq = "delete from table1 where id=? and name=?"//构建sql语句，以？作为占位符，这个位置的值待设置
PreparedStatement ps = con.prepareStatement(sq);    //创建PreparedStatement时就传入sql语句，实现了预编译
 
ps.setString(1,"03");
ps.setString(2,"mao");          //设置sql语句的占位符的值，注意第一个参数位置是1不是0
 
ps.execute();          //执行这个PreparedStatement
 
//设置占位符的值还可以通过setObject(int,object)完成
 
//上面代码的作用时删除表中id=03且name=mao的记录
```

实际开发中，一般采用PreparedStatement访问数据库，它不仅能防止sql注入，还是预编译的(不用改变一次参数就要重新编译整个sql语句，效率高)，此外，它执行查询语句得到的结果集是离线的，连接关闭后，仍然可以访问结果集。


参考：sql注入https://www.cnblogs.com/liuzeyu12a/p/10046228.html

参考链接: https://javaguide.cn/system-design/framework/mybatis/mybatis-interview/







### 相关注解

#### @Mapper和@Repository注解的区别

**@Mapper是mybatis自身带的注解**，有时候使用@Mapper的话，在服务层注入被注释的类的话会会报错，但是不影响使用
也可以使用@Resource ，也可以消除报错

@Repository是spring提供的注释，能够将该类注册成Bean。被依赖注入。 **但是使用该注解后，在启动类上要加@Mapperscan,来表明Mapper类的位置，否则扫描不到**







## SpringBoot相关笔记

### springboot简介

​	在没有SpringBoot出现的时候，Springframework(简称spring)对于j2ee来说，已经是所有开发人员搭建企业级web应用的首选。它的IOC和Aop等新兴概念，极大的提高了开发的效率，追根溯源还是java的东西，在源码当中随处可见各种极具艺术性的设计模式:工厂、代理、单例、观察者、装饰者等等，但是在平时的开发过程当中我们比较经常使用和理解的一般是以IOC为主。将对象的创建与保存交与beanFactory(Spring顶层工厂容器)管理，在需要的地方通过setter、接口、构造器、字段等等方式注入，实现应用的解耦。
原文链接：https://blog.csdn.net/qq_35323137/article/details/105895665

​	Spring Boot让我们的Spring应用变的更轻量化。我们不必像以前那样繁琐的构建项目、打包应用、部署到Tomcat等应用服务器中来运行我们的业务服务。通过Spring Boot实现的服务，只需要依靠一个Java类，把它打包成jar，并通过`java -jar`命令就可以运行起来。这一切相较于传统Spring应用来说，已经变得非常的轻便、简单。

总结一下Spring Boot的主要优点：

- 开箱即用，提供各种默认配置来简化项目配置，约定大于配置的思想。降低了项目搭建的复杂度
- 内嵌式容器简化Web项目，如集成springmvc的webmvcConfiguration
- 没有冗余代码生成和XML配置的要求



### spring与springboot区别

- 简化Web项目的相关配置与整合。如MVC相关配置，如spring需要定义调度程序如 `web.xml` 文件或`Initializer`类，视图解析器等来初始化Spring和SpringMVC的配置。而这些在springboot中都是**通过一个名为auto-configuration的过程添加`Boot web starter`依赖来自动包含的**
- 内嵌tomcat服务器，部署更加方便

- 第三方库的默认配置与开箱即用（自动装配），如thymeleaf模板引擎，mybatis等
- 应用程序启动引导配置
- Spridng Security 配置

https://blog.csdn.net/weixin_39880301/article/details/111017842

简而言之，我们可以说`Spring Boot`只是`Spring`本身的扩展与约定模块集成，使开发，测试和部署更加方便。



### 自动装配原理

​	什么是自动配置？举例来讲，当你通过@Autowired或@Resource注解，自动注入一个类实例之前，被注入进来的这个类实例需要被spring容器纳管，不然肯定会注入失败。往往我们会在xml通过`bean id="dfdf"` 或者在类定义上使用@Component、@Configuration等注解，来实现其被spring容器管理。而对于jar包中的类，则稍微复杂一点，要根据jar包中类的实现进行相应引入。
​	**而spring boot的自动配置功能，会对我们配置的一些类，自动注入到spring容器中。特别是对于依赖的jar包中的一些类，在我们的工程用到这些类实例时，直接@Autowired或@Resource注解注入使用就可以了。**



**Spring Boot的自动配置注解是@EnableAutoConfiguration， 从上面的@Import的类可以找到下面自动加载自动配置的映射。**

这个方法会**加载类路径及所有jar包下META-INF/spring.factories配置**中映射的自动配置的类。

```java
public static List <String> loadFactoryNames

```

​	注解 @EnableAutoConfiguration, @Configuration, @ConditionalOnClass 就是自动配置的核心，首先它得是一个配置文件，其次根据类路径下是否有这个类去自动配置。

具体看这篇文章《[Spring Boot自动配置原理、实战](https://link.segmentfault.com/?enc=KZOM%2FBNs0Fh%2FYdNGTY%2BQYw%3D%3D.fQrLt%2Fq57nn7vACE%2B50OpPMYwgcs1rZ9MD%2FFRXw0cJjze3i2X%2BDgZQh5heuAYT7w89AYN78TVE3ZWxZogQqdog%3D%3D)》。



#### 1、pom.xml

核心依赖在父工程pom中引入了

```properties
<artifactId>spring-boot-starter-parent</artifactId>
```

```properties
<artifactId>spring-boot-dependencies</artifactId>
```

![image-20210816162315232](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/image-20210816162315232.png)

![image-20210816162807003](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/image-20210816162807003.png)



#### 2、核心注解配置

```java
@SpringBootApplication	
	@SpringBootConfiguration
	@EnableAutoConfiguration
		@AutoConfigurationPackage
		@Import(AutoConfigurationImportSelector.class)
	
```

@SpringBootApplication，它是一个复合注解，包括@ComponentScan和@SpringBootConfiguration，@EnableAutoConfiguration



### 常见注解

参考：

https://blog.csdn.net/dmw412724/article/details/89237699

 

### springboot中拦截器的使用

1. 编写拦截器类

   ```java
   /**
    * @Component 和 @Service 是一个作用 创建对象
    */
   @Component
   public class MyInterceptor1 implements HandlerInterceptor {
       Logger logger = LoggerFactory.getLogger(MyInterceptor1.class);
   
       /**
        * 在请求到达Controller控制器之前 通过拦截器执行一段代码
        * 如果方法返回true,继续执行后续操作
        * 如果返回false，执行中断请求处理，请求不会发送到Controller
        */
       @Override
       public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
           logger.info("拦截器1 在控制器执行之前执行");
   
           return true;
       }
   
       /**
        * 控制器之后，跳转前
        */
       @Override
       public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
           logger.info("拦截器1 在控制器执行之后执行");
       }
   
       /**
        * 跳转之后执行
        */
       @Override
       public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
           logger.info("拦截器1 最后执行");
       }
   }
   ```

2. 注册拦截器，配置拦截规则

   ```java
   @Configuration
   public class WebMvcConfig implements WebMvcConfigurer {
       @Autowired
       private MyInterceptor1 myInterceptor1;
   
       /**
        * 注册拦截器
        * @param registry
        */
       @Override
       public void addInterceptors(InterceptorRegistry registry) {
           /**
            * addInterceptor 注册拦截器
            * addPathPatterns 配置拦截规则
            */
           registry.addInterceptor(myInterceptor1)
                   .addPathPatterns("/**");
       }
   }
   ```

   

外链：https://cloud.tencent.com/developer/article/1606373











### SpringBoot默认使用HikariDataSource数据源

**数据源**：<u>存储了所有建立数据库连接的信息。通过提供正确的数据源名称，你可以找到相应的数据库连接。</u>
	数据源负责维持一个数据库连接池，当程序创建数据源实例时，系统会一次性的创建多个数据库连接，并把这些数据连接保存在连接池中。当程序需要进行数据库访问时，无须重新获得数据库连接，而是从连接池中取出一个空闲的数据库连接，当程序使用数据库连接访问结束后，无须关闭数据库连接，而是将数据库连接归还给连接池即可。通过这种方式可以避免频繁的获取数据库连接，关闭数据库连接所导致的性能下降。



1. 有了数据源，就可以拿到数据库连接，使用JdbcTemplate 进行CRUD数据库，即使没有使用第三方数据库操作框架，如：Mybatis，Hibernate,等Spring本身就可以对原生的JDBC做了轻量级的封装，即时JdbcTemplate.
2. SpringBoot不仅提供了默认的数据源，同时默认已经配置好了JdbcTemplate放在容器中，程序员只需自己注入即可使用。
3. JdbcTemplate的自动装配原理是依赖JdbcTemplateAutoConfiguration类。

原文链接：https://blog.csdn.net/qq_41691210/article/details/11073628



## Spring自带的线程池

ThreadPoolTaskExecutor



https://zhuanlan.zhihu.com/p/346086161



## 文件上传流程

以往在使用 Spring 的 web 项目开发中，我们通常会使用 Spring MVC 框架提供的文件上传工具类进行文件上传，也就是 **MultipartResolver** ，利用 SpringMVC 实现文件上传功能，离不开对 MultipartResolver 的设置。

MultipartResolver 这个类，你可以将其视为 SpringMVC 实现文件上传功能时的工具类，这个类也只会在文件上传中发挥作用，在配置了具体实现类之后，SpringMVC 中的 DispatcherServlet 在处理请求时会调用 MultipartResolver 中的方法判断此请求是不是文件上传请求，如果是的话 DispatcherServlet 将调用 MultipartResolver 的 resolveMultipart(request) 方法对该请求对象进行装饰，并返回一个新的MultipartHttpServletRequest 供后继处理流程使用。 注意！此时的请求对象会由 HttpServletRequest 类型转换成 MultipartHttpServletRequest 类型，这个类中会包含所上传的文件对象可供后续流程直接使用，而无需自行在代码中实现对文件内容的读取和对象封装的逻辑。

在 Spring Boot 中也是通过该工具类进行文件上传，与普通的 Spring web 项目不同的是，Spring Boot 在自动配置 DispatcherServlet 时也配置好了 MultipartResolver ，而无需再像原来那样在 Spring MVC 配置文件中增加文件上传配置的 bean。

由于 Spring Boot 自动配置机制的存在，我们并不需要进行多余的设置，只要已经在 pom 文件中引入了 web starter 模块即可直接进行文件上传功能，在前面的实验中我们已经将 web 模块整合到项目中，因此无需再进行整合。虽然不用配置也可以使用文件上传，但是有些开发者可能会在文件上传时有一些特殊的需求

配置含义注释：

- **spring.servlet.multipart.enabled**

  是否支持 multipart 上传文件，默认支持

- **spring.servlet.multipart.file-size-threshold**

  文件大小阈值，当大于这个阈值时将写入到磁盘，否则存在内存中，（默认值 0 ，一般情况下不用特意修改）

- **spring.servlet.multipart.location**

  上传文件的临时目录

- **spring.servlet.multipart.max-file-size**

  最大支持文件大小，默认 1 M ，该值可适当的调整

- **spring.servlet.multipart.max-request-size**

  最大支持请求大小，默认 10 M

- **spring.servlet.multipart.resolve-lazily**

  判断是否要延迟解析文件（相当于懒加载，一般情况下不用特意修改），默认 false

  

#### 上传路径

需要注意的是文件上传路径的设置，我们在代码中设置的文件保存路径为 `/home/project/upload/` 即当前 project 目录下的 upload 文件夹，`/home/project/upload/` 这种写法是 Linux 系统下的路径写法，因为实验楼线上环境的原因我们在代码里使用了这种写法。 如果你本机是 Windows 系统的话，写法与此不同，比如我们想把文件上传到 D 盘下的 upload 文件夹下，就需要把路径设置代码改为

```java
private final static String FILE_UPLOAD_PATH = "D:\\upload\\"
```

这一点需要大家注意，两种系统的写法存在一些差异。



#### Spring Boot 文件上传路径回显

Spring Boot 不像普通的 web 项目可以上传到 webapp 指定目录中，通常的做法是**使用自定义静态资源映射目录，以此来实现文件上传整个流程的闭环**，比如前一小节中的实际案例，在文件上传到 upload 目录后，增加一个自定义静态资源映射，使得 upload 下的静态资源可以通过该映射地址被访问到，新建 config 包，并在包中新增 SpringBootWebMvcConfigurer 类，实现方法如下：

```java
package com.lou.springboot.config;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class SpringBootWebMvcConfigurer implements WebMvcConfigurer {
    public void addResourceHandlers(ResourceHandlerRegistry registry) {		      registry.addResourceHandler("/files/**").addResourceLocations("file:/home/project/upload/");
    }
}
```

通过该设置，所有以 /files/ 开头的静态资源请求都会映射到 /home/project/upload 目录下，与前面上传文件时设置目录类似，不同的系统比如 Linux 和 Windows，文件路径的写法不同。



## Thymeleaf 

### 引入

因为 Spring Boot 官方提供了 Thymeleaf 的场景启动器 `spring-boot-starter-thymeleaf` ，因此可以直接在 pom.xml 文件中添加该场景启动器

```xml
<!-- Thymeleaf 模板引擎依赖 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
```



### Thymeleaf 的标签属性

`th:text` 对应的是 HTML5 中的 text 属性，除 `th:text` 属性外，Thymeleaf 也提供了其他的标签属性来替换 HTML5 原生属性的值，属性节选如下：

![img](https://doc.shiyanlou.com/document-uid18510labid10299timestamp1552982925834.png)

- `th:background` 对应 HTML5 中的背景属性
- `th:class` 对应 HTML5 中的 class 属性
- `th:href` 对应 HTML5 中的链接地址属性
- `th:id` 和 `th:name` 分别对应 HTML5 中的 id 和 name 属性...

`th:block` 比较特殊，它是 Thymeleaf 提供的唯一的一个 Thymeleaf 块级元素，其特殊性在于 Thymeleaf 模板引擎在处理`<th:block>`的时候会删掉它本身，而保留其内容。

这里只列举了部分属性，完整内容可以查看 [thymeleaf-attributes](https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html#setting-value-to-specific-attributes)。



### 标准表达式特性

首先，我们来看看 Thymeleaf 官方对于标准表达式特性的总结：

#### **表达式语法**

- 变量表达式： `${...}`
- 选择变量表达式： `*{...}`
- 信息表达式： `#{...}`
- 链接 URL 表达式： `@{...}`
- 分段表达式： `~{...}`

##### **变量表达式**

变量表达式即 OGNL 表达式或 Spring EL 表达式,作用是获取模板中与后端返回数据所绑定对象的值，写法为 `${...}`，这是最常见的一个表达式，在取值赋值、逻辑判断、循环语句中都可以使用该表达式，示例如下：

```html
<!-- 读取参数 -->
<p>算术运算： 2018+1=<span th:text="${number1}+1">0</span>.</p>
<!-- 读取参数并运算 -->
<div th:class="${path}=='links'?'nav-link active':'nav-link'"></div>
<!-- 读取对象中的属性 -->
<p>
  读取blog对象中title字段：<span th:text="${blog.blogTitle}">default text</span
  >.
</p>
<!-- 循环遍历 -->
<li th:each="blog : ${blogs}"></li>
```

变量表达式也可以使用内置的基本对象：

- ctx : the context object.

- vars : the context variables.

- locale : the context locale.

- request : web 环境下的 HttpServletRequest 对象.

- response :web 环境下的 HttpServletResponse 对象 .

- session : web 环境下的 HttpSession 对象.

- servletContext : web 环境下的 ServletContext 对象.

  示例如下：

  ```html
  <p>
    读取内置对象中 request 中的内容：<span
      th:text="${#request.getAttribute('requestObject')}"
      >default text</span
    >.
  </p>
  <p>
    读取内置对象中 session 中的内容：<span
      th:text="${#session.getAttribute('sessionObject')}"
      >default text</span
    >.
  </p>
  ```

  

同时，Thymeleaf 还提供了一系列 Utility 工具对象（内置于 Context 中），可以通过 `#` 直接访问，工具类如下：

- dates ： *java.util.Date 的功能方法类。*
- calendars : *类似 #dates，面向 java.util.Calendar*
- numbers : *格式化数字的工具方法类*
- strings : *字符串对象的工具方法类，contains,startWiths,prepending/appending 等等。*
- bools：*对布尔值求值的工具方法。*
- arrays：*对数组的工具方法。*
- lists：*对 java.util.List 的工具方法*
- sets：*对 java.util.Set 的工具方法*
- maps：*对 java.util.Map 的工具方法*

你可以将这些方法视为工具类，通过这些方法可以使得 Thymeleaf 在操作变量时更加方便。



##### 选择(星号)表达式

选择表达式与变量表达式类似，不过它会用一个预先选择的对象来代替上下文变量容器(map)来执行，语法如下： `*{blog.blogId}`，被指定的对象由 th:object 标签属性进行定义，前文中读取 blog 对象的 title 字段可以替换为：

```html
<p th:object="${blog}">
  读取blog对象中title字段：<span th:text="*{blogTitle}">text</span>.
</p>
```

如果不考虑上下文的情况下，两者没有区别，使用 `${...}`读取的内容也完全可以替换为使用`*{...}`进行读取，唯一的区别是使用`*{...}`前可以预先在父标签中通过 `th:object` 定义一个对象并进行操作。

```html
<p>
  读取blog对象中title字段：<span th:text="*{blog.blogTitle}">default text</span>
</p>
<p>读取text字段：<span th:text="*{text}">default text</span>.</p>
```



##### URL 表达式

`th:href` 对应的是 html 中的 href 标签，它将计算并替换 href 标签中的链接 URL 地址，`th:href` 中可以直接设置为静态地址，也可以使用表达式语法读取到的变量值进行动态拼接 URL 地址。

比如一个详情页 URL 地址：`http://localhost:8080/blog/1`，当使用 URL 表达式时，可以写成这样：

```html
<a th:href="@{'http://localhost:8080/blog/1'}">详情页</a>
```

也可以根据 id 值进行替换，写法为：

```html
<a th:href="@{'/blog/'+${blog.blogId}}">详情页</a>
```

或者也可以写成这样：

```html
<a th:href="@{/blog/{blogId}(blogId=${blog.blogId})">详情页</a>
```

以上三种表达式写法生成 URL 的最终结果都是相同的，开发者可以自己使用字符串拼接的方法组装 URL (第二种写法)，也可以使用 URL 表达式提供的语法进行 URL 组装（第三种写法）。如果有多个参数可以自行拼装字符串，或者使用逗号进行分隔，写法如下：

```html
<a
  th:href="@{/blog/{blogId}(blogId=${blog.blogId},title=${blog.blogTitle},tag='java')}"
  >详情页</a>
```

最终生成的 URL 为 `http://localhost:8080/blog/1?title=lou-springboot&tag=java` 另外，URL 中以 "/" 开头的路径(比如 /blog/1 )，默认生成的 URL 会加上项目的当前地址形成完整的 URL 。



#### 常规语法

- **字面量**

  - 字符串： 'one text', 'Another one!' ...
  - 数字： `0`, `34`, `3.0`, `12.3` ...
  - 布尔值： `true`, `false`
  - Null 值： `null`
  - 字面量标记：`one`, `sometext`, `main` ...

- **文本运算**

  - 字符串拼接： `+`
  - 字面量置换: `|The name is ${name}|`

- **算术运算**

  - 二元运算符： `+`, `-`, `*`, `/`, `%`
  - 负号（一元运算符）： (unary operator): `-`

- **布尔运算**

  - 二元运算符： `and`, `or`
  - 布尔非（一元运算符）： `!`, `not`

- **比较运算**

  - 比较： `>`, `<`, `>=`, `<=` (`gt`, `lt`, `ge`, `le`)
  - 相等运算符： `==`, `!=` (`eq`, `ne`)

  比较运算符也可以使用转义字符，比如大于号，可以使用 Thymeleaf 语法 `gt` 也可以使用转义字符`>`

- **条件运算符**

  - If-then: `(if) ? (then)`
  - If-then-else: `(if) ? (then) : (else)`
  - Default: `(value) ?: (defaultvalue)`

- **特殊语法**

  - 无操作： `_`

  

### Thymeleaf 模板引擎使用注意事项

- 必须引入名称空间

  ```html
  <html lang="en" xmlns:th="http://www.thymeleaf.org"></html>
  ```

  即使不引入以上名称空间，静态资源访问以及模板动态访问都不会报错，因此有些开发者可能会忽略这个事情。但是建议在开发过程中最好引入该名称空间，因为引入之后会有 Thymeleaf 代码的语法提示，能够提升开发效率，也减少人为造成的低级错误。

  > 这里引入空间名是需要访问外网的，所有如果非会员用户运行代码效果显示不出来，请自行在本地尝试。

- 禁用模板缓存

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid18510labid10298timestamp1552982767807.png)

Thymeleaf 的默认缓存设置是通过配置文件的 **spring.thymeleaf.cache** 配置属性决定的，通过如上 Thymeleaf 模板的配置属性类 ThymeleafProperties 可以发现该属性默认为 true，因此 Thymeleaf 默认是使用模板缓存的，该设置有助于改善应用程序的性能，因为模板只需编译一次即可，但是在开发过程中不能实时看到页面变更的效果，除非重启应用程序，因此建议将该属性设置为 false，在配置文件中修改如下：

```xml
spring.thymeleaf.cache=false
```

- IDEA 中通过 Thymeleaf 语法读取变量时爆红色波浪线问题

  如下图所示，在刚开始使用 Thymeleaf 开发时可能会碰到这种问题，在模板文件中通过 Thymeleaf 语法读取变量时，该变量名称下会出现红色波浪线，也就是错误的标志。

  ![此处输入图片的描述](https://doc.shiyanlou.com/document-uid18510labid10298timestamp1552982790597.png)

  如果不熟的朋友可能会人为自己的模板文件有问题，但其实并不是那么严重，只是由于 IDEA 中默认开启了表达式参数验证，即使在后端的 model 数据中添加了该变量，但是对于前端文件是无法感知的，因此会报这个错误，可以在 IDEA 中默认将验证关闭或者将提示级别修改掉也可以。

  ![此处输入图片的描述](https://doc.shiyanlou.com/document-uid18510labid10298timestamp1552982808053.png)







## SpringBoot整合MyBatis操作数据库

### mybatis-springboot-starter 介绍

MyBatis 整合 Spring Boot 项目时的场景启动器，**mybatis-springboot-starter** 。

- 构建独立的 MyBatis 应用程序
- 零模板
- 更少的 XML 配置代码甚至无 XML 配置

其官网地址为 [mybatis-spring-boot](http://www.mybatis.org/spring-boot-starter/mybatis-spring-boot-autoconfigure/index.html)



### application.properties 配置

- **mybatis.config-location**

  配置 mybatis-config.xml 路径，mybatis-config.xml 中配置 MyBatis 基础属性，如果项目中配置了 mybatis-config.xml 文件需要设置该参数

- **mybatis.mapper-locations**

  配置 Mapper 文件对应的 XML 文件路径

- **mybatis.type-aliases-package**

  配置项目中实体类包路径

```xml
mybatis.config-location=classpath:mybatis-config.xml
mybatis.mapper-locations=classpath:mapper/*Dao.xml
mybatis.type-aliases-package=com.lou.springboot.entity
```

我们只配置 mapper-locations 即可，最终的 application.properties 文件如下：

```properties
# datasource config
spring.datasource.url=jdbc:mysql://localhost:3306/lou_springboot?useUnicode=true&characterEncoding=utf8&autoReconnect=true&useSSL=false
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.username=root
spring.datasource.password=

mybatis.mapper-locations=classpath:mapper/*Dao.xml
```



### Mybatis-Generator自动生成代码

#### MyBatis-Generator 介绍

MyBatis Generator 是 MyBatis 官方提供的代码生成器插件，可以用于 MyBatis 和 iBatis 框架的代码生成，支持所有版本的 MyBatis 框架以及 2.2.0 版本及以上的 iBatis 框架。

在前文中我们也介绍了如何使用 MyBatis 进行数据库操作，<u>在进行功能开发时，一张表我们需要编写实体类、DAO 接口类以及 Mapper 文件，这些是必不可少的文件，如果表的数量较大，我们就需要重复的去创建实体类、Mapper 文件以及 DAO 类并且需要配置它们之间的依赖关系，这无疑是一个很麻烦的事情</u>，**而 MyBatis Generator 插件就可以帮助我们去完成这些开发步骤，使用该插件可以帮助开发者自动去创建实体类、Mapper 文件以及 DAO 类并且配置好它们之间的依赖关系，我们可以直接在项目中使用这些代码，后续只需要关注业务方法的开发即可。**





## Spring Boot 中的事务处理

### 数据库事务简介

数据库事务是指作为单个逻辑工作单元执行的一系列操作，要么完全地执行，要么完全地不执行。 事务处理可以确保除非事务性单元内的所有操作都成功完成，否则不会永久更新面向数据的资源。

通过将一组相关操作组合为一个要么全部成功要么全部失败的单元，可以简化错误恢复并使应用程序更加可靠。<u>一个逻辑工作单元要成为事务，必须满足所谓的 ACID（原子性、一致性、隔离性和持久性）属性，事务是数据库运行中的逻辑工作单位，由数据库中的事务管理子系统负责事务的处理。</u>



### 事务管理实现机制

![image-20210914213113850](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/image-20210914213113850.png)



### 声明式事务

声明式事务是建立在 AOP 机制之上的，其本质是对方法前后进行拦截，然后在目标方法开始之前创建或者加入一个事务，在执行完目标方法之后根据执行情况提交或者回滚事务。

声明式事务最大的优点，就是通过 AOP 机制将具体业务逻辑与事务处理解耦，不需要通过编程的方式管理事务，这样就不需要在业务逻辑代码中掺杂事务管理的代码，因此在实际使用中声明式事务用的比较多。

声明式事务有两种方式：一种是在 XML 配置文件中做相关的事务规则声明；另一种是基于 `@Transactional` 注解的方式（`@Transactional` 注解是来自 `org.springframework.transaction.annotation` 包），便可以将事务规则应用到业务逻辑中。

#### 未使用 Spring Boot 时的事务配置

下面这个配置文件是普通的 SSM 框架整合时的事务配置，相信大家都比较熟悉这段配置代码：

```xml
    <!-- 事务管理 -->
    <bean id="transactionManager"
          class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!-- 配置事务通知属性 -->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <!-- 定义事务传播属性 -->
        <tx:attributes>
            <tx:method name="insert*" propagation="REQUIRED"/>
            <tx:method name="import*" propagation="REQUIRED"/>
            <tx:method name="update*" propagation="REQUIRED"/>
            <tx:method name="upd*" propagation="REQUIRED"/>
            <tx:method name="add*" propagation="REQUIRED"/>
            <tx:method name="set*" propagation="REQUIRED"/>
            <tx:method name="remove*" propagation="REQUIRED"/>
            <tx:method name="delete*" propagation="REQUIRED"/>
            <tx:method name="get*" propagation="REQUIRED" read-only="true"/>
            <tx:method name="*" propagation="REQUIRED" read-only="true"/>
        </tx:attributes>
    </tx:advice>

    <!-- 配置事务切面 -->
    <aop:config>
        <aop:pointcut id="serviceOperation"
                      expression="(execution(* com.ssm.demo.service.*.*(..)))"/>
        <aop:advisor advice-ref="txAdvice" pointcut-ref="serviceOperation"/>
    </aop:config>
```

通过这段代码我们也能够看出声明式事务的配置过程：

1. 配置事务管理器
2. 配置事务通知属性
3. 配置事务切面

这样配置后，相关方法在执行时都被纳入事务管理下了，一旦发生异常，事务会正确回滚。



### Spring Boot 项目中的事务控制

> 在 SpringBoot 中，建议采用注解 `@Transactional` 进行事务的控制，只需要在需要进行事务管理的方法或者类上添加 `@Transactional` 注解即可

新建 TransactionTestService.java

首先新建 service 包作为业务代码包，事务处理一般在 service 层做，当然在 controller 层中处理也可以，但是建议还是在业务层进行处理，之后在包中新建 TransactionTestService 类，代码如下：

```java
package com.lou.springboot.service;
import com.lou.springboot.dao.UserDao;
import com.lou.springboot.entity.User;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;
import javax.annotation.Resource;

@Service
public class TransactionTestService {
    @Resource
    UserDao userDao;

    public Boolean test1() {
        User user = new User();
        user.setPassword("test1-password");
        user.setName("test1");
        // 在数据库表中新增一条记录
        userDao.insertUser(user);
        // 发生异常
        System.out.println(1 / 0);
        return true;
    }

    @Transactional
    public Boolean test2() {
        User user = new User();
        user.setPassword("test2-password");
        user.setName("test2");
        // 在数据库表中新增一条记录
        userDao.insertUser(user);
        // 发生异常
        System.out.println(1 / 0);
        return true;
    }
}
```

首先在类上添加 `@Service` 将其注册到IOC 容器中管理，之后注入 UserDao 对象以实现后续的数据层操作，最后实现两个业务方法 `test1()` 和 `test2()`，二者实现类似，只是两个方法添加的用户对象名称和密码字符串不同，且 `test2()` 方法上添加了 `@Transactional` 注解，而 `test1()` 方法上并没有添加，在方法中我们都添加了一句代码，让数字 1 去除以数字 0，这段代码一定会出现异常，我们用这个来模拟在发生异常时事务处理能否成功。

按照正常理解，在执行 SQL 语句后，一旦发生异常，这次数据库更改一定会被事务进行回滚，正常情况下数据库中会有 `test1` 的数据而没有 `test2` 的数据，因为 `test1()` 方法并没有纳入事务管理中，而 `test2()` 方法由于加上了 `@Transactional` 注解是会被事务管理器处理的，那么接下来我们就来执行两个业务层方法，看看数据库中的数据变化。



### Spring Boot 事务管理器自动配置

在讲解声明式事务时，我们提到了声明式事务的配置过程，首先需要配置事务管理器，但是我们在开发时并没有进行该管理器的配置但是事务管理却起到了作用，这是为什么呢？

答案是 Spring Boot 在启动过程中已经将该对象自动配置完成了，<u>所以我们在 Spring Boot 项目中可以直接使用 `@Transactional` 注解来处理事务</u>，感兴趣的同学可以查看源码进行学习，事务管理器的自动配置类如下：

- org.springframework.boot.autoconfigure.jdbc.DataSourceTransactionManagerAutoConfiguration
- org.springframework.boot.autoconfigure.transaction.TransactionAutoConfiguration

可以在你本地开发工具 (IDEA 或者 Eclipse) 中搜索这两个类的源码来看。



### @Transactional 事务实现机制

**`@Transactional 不仅可以注解在方法上，也可以注解在类上。当注解在类上时，意味着此类的所有 public 方法都是开启事务的。如果类级别和方法级别同时使用了 @Transactional 注解，则使用在类级别的注解会重载方法级别的注解。`**

<u>在应用系统调用声明了 `@Transactional` 的目标方法时，Spring Framework 默认使用 AOP 代理，在代码运行时生成一个**代理对象**</u>，根据 `@Transactional` 的属性配置信息，这个代理对象决定该声明 `@Transactional` 的目标方法是否由拦截器 TransactionInterceptor 来使用拦截；在 TransactionInterceptor 拦截时，会在目标方法开始执行之前创建并加入事务，并执行目标方法的逻辑, 最后根据执行情况是否出现异常，利用抽象事务管理器 AbstractPlatformTransactionManager 操作数据源 DataSource 提交或回滚事务。

**注解：隔离级别，传播机制，异常类型等使用方式**

```java
@Transactional(isolation = Isolation.READ_COMMITTED, propagation = Propagation.REQUIRED, rollbackFor = Exception.class)
public int addComment(Comment comment) {
    
}


```

@Transactional 属性配置

![img](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/1460902-20190214093944549-1933776061-1638383846695.png)



注意事项：

- @Transactional 注解应该只被应用到 public 方法上，这是由 Spring AOP 的本质决定的。在 protected、private 或者默认可见性的方法上使用 @Transactional 注解，这将被忽略，也不会抛出任何异常

- 默认情况下，只有来自外部的方法调用才会被AOP代理捕获，也就是，类内部方法调用本类内部的其他方法并不会引起事务行为，即使被调用方法使用了@Transactional注解进行修饰



### Spring事务回滚机制

再说下声明式事务和注解事务回滚的原理：**当被切面切中或者是加了注解的方法中抛出了RuntimeException异常时，Spring会进行事务回滚**。默认情况下是捕获到方法的RuntimeException异常，也就是说**抛出只要属于运行时的异常（即RuntimeException及其子类）都能回滚**；但当抛出一个不属于运行时异常时，事务是不会回滚的。

https://www.cnblogs.com/zeng1994/p/8257763.html



### 事务的传播机制

参考：https://blog.csdn.net/qq_35493807/article/details/105756761

https://segmentfault.com/a/1190000015794446?utm_source=sf-similar-article

事务的传播行为是针对嵌套事务而言。即是针对（需要事务的业务方法）调用（需要事务的业务方法）。

当事务方法被另一个事务方法调用时，必须指定事务应该如何传播。例如：方法可能继续在现有事务中运行，也可能开启一个新事务，并在自己的事务中运行。

Spring 定义了 7 种类传播行为。

![image-20211202004023195](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/image-20211202004023195-1638376825150.png)



#### Propagation.REQUIRED

注意: **@Transactional** 没有指明 **propagation** 属性, 取默认值 **Propagation.REQUIRED**

含义为: 要求的, 必须的. 如果被注解的方法有这个传播属性, 它的行为是:

1. 如果它作为一个子事务方法, 在其他事务方法中被调用, 那么该方法不会创建新的事务, 使用现有的父级别的事务.
2. 如果它作为一个子事务方法, 没有在其他事务方法中被调用, 而是在非事务方法中直接调用, 那么它会创建一个新的事务来执行数据库操作.

**归纳：** 要求的. 能够保证方法调用操作总是在一个事务中进行的.

**应用场景**: 不知道方法的调用者是否创建了事务, 但是要求当前被调用的方法必须在一个事务当中执行.



#### **Propagation.REQUIRES_NEW**

这个机制下的事务将被完全 **提交** 或 **回滚** 而不依赖于外部事务，它拥有自己的隔离范围，自己的锁, 等等。当内部事务开始执行时, 外部事务将被挂起, 内务事务结束时, 外部事务将继续执行. 常见于事务步骤 **要求时序** 的情况，如：开启一个全新的事务, 一般用于分布式事务中的一个前期步骤. 比如一键购功能. 主要步骤包括:

- **创建订单**
  - 创建订单
  - 扣除库存
  - 创建物流订单
- **发起支付**

**归纳: 会创建自身独立的事务，内层事务可以独立回滚, 不影响外层事务.**

**前提是外层事务的方法不能和内层事务的方法在同一个服务类中，否则事务机制会失效**。



#### spring同类调用事务不生效-原因

因为代理类调用方法的原因如下：

@Transcational 和 AOP代理

```
在代理bean对象（Proxy类代理CGLIB）中，被代理类内部方法互相调用时，实则调用的是代理对象的原始对象（不属于 spring bean）的方法。便不会走代理类在执行目标方法前后，加上的事务管理的代码（也可以说增强的代码）所以调用方法时不会去判断方法上的注解。
```

结合代理模式简单理解记忆：

![image-20211201235905470](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/image-20211201235905470-1638374346757.png)

如以下代码，**Spring创建的代理对象也会实现StudentService接口，代理类便在同样的方法中前后执行事务管理的代码**

```java
 public class StudentServiceImpl implements StudentService {
  
     @Autowired
     private StudentMapper studentMapper;
  
     @Override
     public void insertStudent(){
         insert();
     }
  
     @Transactional(rollbackFor = Exception.class)
     public void insert() {
         StudentDO studentDO = new StudentDO();
         studentDO.setName("小民");
         studentDO.setAge(22);
         studentMapper.insert(studentDO);
  
         if (studentDO.getAge() > 18) {
             throw new RuntimeException("年龄不能大于18岁");
         }
     }
 }
```

如何解决？

https://blog.csdn.net/hellozhxy/article/details/109753711

方式一：注入自己，获取到spring的代理对象再进行调用

```java
@Service
public class StudentServiceImpl implements StudentService {
 
    @Autowired
    private StudentMapper studentMapper;
 
    @Autowired
    private StudentService studentService;
 
    @Override
    public void insertStudent(){
        studentService.insert();
    }
 
    @Override
    @Transactional(rollbackFor = Exception.class)
    public void insert() {
        StudentDO studentDO = new StudentDO();
        studentDO.setName("小民");
        studentDO.setAge(22);
        studentMapper.insert(studentDO);
 
        if (studentDO.getAge() > 18) {
            throw new RuntimeException("年龄不能大于18岁");
        }
    }
}
```

参考：https://blog.csdn.net/hellozhxy/article/details/109753711



#### **@Transactional(propagation=Propagation.MANDATORY)**

必须在一个已有的事务中执行,否则抛出异常



#### Propagation.NEVER

**如果当前存在事务, 则抛出异常, 否则在无事务环境上执行代码**，与mandatory相反。。



#### **@Transactional(propagation=Propagation.NOT_SUPPORTED)**

**以非事务方式执行操作, 如果当前存在事务, 就把当前事务挂起**，即容器不为这个方法开启事务



#### **@Transactional(propagation=Propagation.SUPPORTS)**

如果其他bean调用这个方法,在其他bean中声明事务,那就用事务.如果其他bean没有声明事务,那就不用事务.

**归纳: 有没有父级事务(外层事务)都可以接受**



## 项目实践之 Ajax 技术使用教程

### Ajax 简介

AJAX = Asynchronous JavaScript and XML（异步的 JavaScript 和 XML），它是一种用于创建快速动态网页的技术，通过浏览器与服务器进行少量数据交换，Ajax 可以使网页实现异步更新，这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新，传统的网页如果需要更新内容，必须要进行跳转并重新加载整个网页。

Ajax 技术使得网站与用户间有了更友好的交互效果，比较常见的借助 Ajax 技术实现的功能有*列表上拉加载分页数据*、*省市区三级联动*、*进度条更新等等*，这些都是借助 Ajax 技术在当前页面即可完成的功能，**即使有数据交互也不会跳转页面，整体交互效果有了很大的提升**。



### Ajax 工作流程

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid441493labid8432timestamp1550025745383.png)

Ajax 的整个工作流程如上图所示，**用户在进行页面上进行操作时会执行 js 方法，js 方法中通过 Ajax 异步与后端进行数据交互**。**首先会创建 XMLHttpRequest 对象，XMLHttpRequest 是 AJAX 的基础，之后会根据页面内容将参数封装到请求体中或者放到请求 URL 中，然后向后端服务器发送请求，请求成功后根据后端返回的数据进行解析和部分逻辑处理，最终在不刷新页面的情况下对页面进行局部更新。**



### 其他学习网址

[jQuery 参考手册 - Ajax](https://www.w3school.com.cn/jquery/jquery_ref_ajax.asp)

[原生AJAX和jQuery ajax的使用和区别](https://blog.csdn.net/liyifan687/article/details/80420443)

 [jquery中ajax请求的使用和四个步骤示例](https://www.cnblogs.com/enumx/p/12326676.html)

### 前端页面编码进行测试

在 resources/static 下新建 ajax-test.html，代码如下：

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <title>lou.SpringBoot | Ajax 请求测试</title>
  </head>
  <body class="hold-transition login-page">
    <div style="width:720px;margin:7% auto">
      <div class="content">
        <div class="container-fluid">
          <div class="row">
            <div class="col-lg-6">
              <div class="card">
                <div class="card-header">
                  <h5 class="m-0">接口测试1</h5>
                </div>
                <div class="card-body">
                  <input
                    type="text"
                    id="info"
                    class="form-control"
                    placeholder="请输入info值"
                  />
                  <h6 class="card-title">接口1返回数据如下：</h6>
                  <p class="card-text" id="test1"></p>
                  <a href="#" class="btn btn-primary" onclick="requestTest1()"
                    >发送请求1</a
                  >
                </div>
              </div>
              <br />
              <div class="card card-primary card-outline">
                <div class="card-header">
                  <h5 class="m-0">接口测试2</h5>
                </div>
                <div class="card-body">
                  <h6 class="card-title">接口2返回数据如下：</h6>
                  <p class="card-text" id="test2"></p>
                  <a href="#" class="btn btn-primary" onclick="requestTest2()"
                    >发送请求2</a
                  >
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </body>
</html>
```

ajax-test 页面上分别定义了两个按钮，在点击时分别会分别触发 `onclick` 点击事件，在点击事件的实现逻辑中进行页面内容的局部更新。

### Ajax 调用逻辑

由于原生 Ajax 实现比较繁琐，实际开发中一般都会使用 jQuery 封装的 Ajax 方法，或者其他库封装的 Ajax 方法，详细可以参考 [jQuery ajax 讲解](http://www.w3school.com.cn/jquery/ajax_ajax.asp)。

在调用前首先在 html 代码中引入 jQuery 库，之后根据点击事件分别定义两个事件触发的 js 方法：`requestTest1()` 和 `requestTest2()`，在方法中分别使用 Ajax 向后端发送请求，在请求成功后将响应结果赋值到对应的 div 中，代码如下：

```html
<!-- 引入jQuery -->
<script src="https://cdn.staticfile.org/jquery/1.12.0/jquery.min.js"></script>

<!-- 定义两个点击事件并实现 Ajax 调用逻辑 -->
<script type="text/javascript">
  function requestTest1() {
    var info = $('#info').val();
    $.ajax({
      type: 'GET', //方法类型
      dataType: 'text', //预期服务器返回的数据类型
      url: 'api/test1?info=' + info, //请求地址
      contentType: 'application/json; charset=utf-8',
      success: function (result) {
        //请求成功后回调
        $('#test1').html(JSON.stringify(result));
      },
      error: function () {
        //请求失败后回调
        $('#test1').html('接口异常，请联系管理员！');
      },
    });
  }
  function requestTest2() {
    $.ajax({
      type: 'GET', //方法类型
      dataType: 'json', //预期服务器返回的数据类型
      url: 'api/test2',
      contentType: 'application/json; charset=utf-8',
      success: function (result) {
        $('#test2').html(JSON.stringify(result));
      },
      error: function () {
        $('#test2').html('接口异常，请联系管理员！');
      },
    });
  }
</script>
```

方法一中会首先获取用户在 input 框中输入的字段，之后将其拼接到请求的 URL 中，最后发送 Ajax 请求并完成回调，方法二中也是类似，用户点击发送请求的按钮后，会触发 `onclick` 点击事件并调用 `requestTest2()` 方法，在请求完成后进入 success 回调方法，并将请求结果的内容放到 div 中显示。



### 后端接口实现

在包 `com.lou.springboot.controller` 下新建 RequestTestController，代码如下：

```java
package com.lou.springboot.controller;

import com.lou.springboot.entity.User;
import org.springframework.util.StringUtils;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

import java.util.ArrayList;
import java.util.List;

@RestController
@RequestMapping("/api")
public class RequestTestController {
    @RequestMapping(value = "/test1", method = RequestMethod.GET)
    public String test1(String info) {
        if (StringUtils.isEmpty(info) || StringUtils.isEmpty(info)) {
            return "请输入info的值！";
        }
        return "你输入的内容是:" + info;
    }
    @RequestMapping(value = "/test2", method = RequestMethod.GET)
    public List<User> test2() {
        List<User> users = new ArrayList<>();
        User user1 = new User();
        user1.setId(1);
        user1.setName("十一");
        user1.setPassword("12121");
        User user2 = new User();
        user2.setId(2);
        user2.setName("十二");
        user2.setPassword("21212");
        User user3 = new User();
        user3.setId(3);
        user3.setName("十三");
        user3.setPassword("31313");
        users.add(user1);
        users.add(user2);
        users.add(user3);
        return users;
    }
}
```

控制器中分别实现两个方法并设置请求地址映射来处理前端发送的异步请求，`test1()` 方法中会根据前端传入的 info 字段值重新返回给前端一个字符串，请求方式为 GET，`test2()` 方法中会返回一个集合对象给前端，请求方式为 GET。

在前后端对接时需要确定好请求路径、请求方法、返回结果的格式等，比如后端定义的请求方法为 GET，那么在 Ajax 设置时一定要将 type 设置为 GET，不然无法正常的发起请求，`test1()` 方法和 `test2()` 方法中返回的结果格式也不同，所以在前端进行 Ajax 调用时 dataType 也需要注意，比如本案例中分别返回的是字符串类型和集合类型，那么在 Ajax 请求中需要将 dataType 分别设置为 text 和 json，不然前端在 Ajax 调用后会直接进入 error 回调中，而不是 success 成功回调中。

> 测试项目：springboot-01-firstproject



## 项目实践之 RESTful API 设计与实现

### 实验介绍

#### 实验内容

**在实际的项目开发中，进行至接口设计阶段时，后端开发人员和前端开发人员都会参与其中，根据已制定的规范对接口进行设计和返回数据格式的约定（不同项目组规范可能不同）**，但是像前一个实验中的情况应该不会出现，接口的请求方式不会仅仅只有 GET 方式，返回结果的数据格式反而会比较统一，返回结果一般会进行封装。本篇文章将会对 api 设计及数据规范进行简单的介绍，之后结合实际案例对数据交互进行编码实现。

#### 实验知识点

- RESTful api 设计规范
- RESTful api 数据规范
- Ajax + RESTful api 前后端交互实践



### RESTful api 设计规范

目前比较流行的一套接口规范就是 RESTful api，REST（Representational State Transfer）,中文翻译叫"表述性状态转移",它首次出现在 2000 年 Roy Fielding 的博士论文中，Roy Fielding 是 HTTP 规范的主要编写者之一。他在论文中提到："我这篇文章的写作目的，就是想在符合架构原理的前提下，理解和评估以网络为基础的应用软件的架构设计，得到一个功能强、性能好、适宜通信的架构。REST 指的是一组架构约束条件和原则。"

如果一个架构符合 REST 的约束条件和原则，我们就称它为 RESTful 架构，REST 其实并没有创造新的技术、组件或服务，在我的理解中，它更应该是一种理念、一种思想，利用 Web 的现有特征和能力，更好地诠释和体现现有 web 标准中的一些准则和约束。

#### 基本原则一：URI

- 应该将 api 部署在专用域名之下。
- URL 中尽量不用大写。
- URI 中不应该出现动词，动词应该使用 HTTP 方法表示但是如果无法表示，也可使用动词，例如：search 没有对应的 HTTP 方法,可以在路径中使用 search，更加直观。
- URI 中的名词表示资源集合，使用复数形式。
- URI 可以包含 queryString，避免层级过深。

#### 基本原则二：HTTP 动词

对于资源的具体操作类型，由 HTTP 动词表示，常用的 HTTP 动词有下面五个：

- GET：从服务器取出资源（一项或多项）。
- POST：在服务器新建一个资源。
- PUT：在服务器更新资源（客户端提供改变后的完整资源）。
- PATCH：在服务器更新资源（客户端提供改变的属性）。
- DELETE：从服务器删除资源。

还有两个不常用的 HTTP 动词：

- HEAD：获取资源的元数据。
- OPTIONS：获取信息，关于资源的哪些属性是客户端可以改变的。

例子：

```
用户管理模块：
1. [POST]   http：//lou.springboot.tech/users   // 新增
2. [GET]    http：//lou.springboot.tech/users?page=1&rows=10 // 列表查询
3. [PUT]    http：//lou.springboot.tech/users/12 // 修改
4. [DELETE] http：//lou.springboot.tech/users/12 // 删除
```

#### 基本原则三：状态码（Status Codes）

处理请求后，服务端需向客户端返回的状态码和提示信息。

常见状态码**(状态码可自行设计，只需开发者约定好规范即可)**：

- 200：SUCCESS 请求成功。
- 401：Unauthorized 无权限。
- 403：Forbidden 禁止访问。
- 410：Gone 无此资源。
- 500：INTERNAL SERVER ERROR 服务器发生错误。 ...

#### 基本原则四：错误处理

如果服务器发生错误或者资源不可达，应该向用户返回出错信息。

#### 基本原则五：服务端数据返回

后端的返回结果最好使用 JSON 格式，且格式统一。

#### 基本原则六：版本控制

- 规范的 api 应该包含版本信息，在 RESTful api 中，最简单的包含版本的方法是将版本信息放到 url 中，如：

```
[GET]    http：//lou.springboot.tech/v1/users?page=1&rows=10
[PUT]    http：//lou.springboot.tech/v1/users/12
```

- 另一种做法是，使用 HTTP header 中的 accept 来传递版本信息。

以下为接口安全原则的注意事项：

#### 安全原则一：Authentication 和 Permission

Authentication 指用户认证，Permission 指权限机制，这两点是使 RESTful api 强大、灵活和安全的基本保障。

常用的认证机制是 Basic Auth 和 OAuth，RESTful api 开发中，除非 api 非常简单，且没有潜在的安全性问题，否则，**认证机制是必须实现的**，并应用到 api 中去。Basic Auth 非常简单，很多框架都集成了 Basic Auth 的实现，自己写一个也能很快搞定，OAuth 目前已经成为企业级服务的标配，其相关的开源实现方案非常丰富。

#### 安全原则二：CORS

CORS 即 Cross-origin resource sharing，在 RESTful api 开发中，主要是为 js 服务的，解决调用 RESTful api 时的跨域问题。

由于固有的安全机制，js 的跨域请求时是无法被服务器成功响应的。现在前后端分离日益成为 web 开发主流方式的大趋势下，后台逐渐趋向指提供 api 服务，为各客户端提供数据及相关操作，而网站的开发全部交给前端搞定，网站和 api 服务很少部署在同一台服务器上并使用相同的端口，js 的跨域请求时普遍存在的，开发 RESTful api 时，通常都要考虑到 CORS 功能的实现，以便 js 能正常使用 api。

目前各主流 web 开发语言都有很多优秀的实现 CORS 的开源库，我们在开发 RESTful api 时，要注意 CORS 功能的实现，直接拿现有的轮子来用即可。





### 返回结果封装

在前一个实验中，我们使用了 Ajax 技术进行后端接口的调用，但是返回结果的数据格式并不统一，这在实际的项目开发工作中一般不会出现，因此我们首先将返回结果进行抽象并封装。

新建 common 包，并封装 Result 结果类，代码如下（注：代码位于 com.lou.springboot.common）：

```java
package com.lou.springboot.common;

import java.io.Serializable;

public class Result<T> implements Serializable {
    private static final long serialVersionUID = 1L;
    //业务码，比如成功、失败、权限不足等 code，可自行定义
    private int resultCode;
    //返回信息，后端在进行业务处理后返回给前端一个提示信息，可自行定义
    private String message;
    //数据结果，泛型，可以是列表、单个对象、数字、布尔值等
    private T data;

    public Result() {
    }

    public Result(int resultCode, String message) {
        this.resultCode = resultCode;
        this.message = message;
    }
    // 省略部分代码
}
```

每一次后端数据返回都会根据以上格式进行数据封装，包括**业务码、返回信息、实际的数据结果**，而不是像前一个实验中的不确定格式，前端接受到该结果后对数据进行解析，并通过业务码进行相应的逻辑操作，之后再将 data 中的数据获取到并进行页面渲染或者进行信息提示。

实际返回的数据格式如下：

- 列表数据

```json
{
  "resultCode": 200,
  "message": "SUCCESS",
  "data": [
    {
      "id": 2,
      "name": "user1",
      "password": "123456"
    },
    {
      "id": 1,
      "name": "13",
      "password": "12345"
    }
  ]
}
```

- 单条数据

```json
{
  "resultCode": 200,
  "message": "SUCCESS",
  "data": true
}
```

如上两个分别是列表数据和单条数据的返回，后端进行业务处理后将会返回给前端一串 json 格式的数据，resultCode 等于 200 表示数据请求成功，该字段也可以自行定义，比如 0、1001、500 等等，message 值为 SUCCESS，也可以自行定义返回信息，比如“获取成功”、“列表数据查询成功”等，这些都需要与前端约定好，一个码只表示一种含义，而 data 中的数据可以是一个对象数组、也可以是一个字符串、数字等类型，根据不同的业务返回不同的结果，之后的实践内容里都会以这种方式返回数据。



### 后端接口实现

我们会按照 api 规范进行接口设计和接口调用，以对 tb_user 表进行增删改查为例进行实践，在`com.lou.springboot.controller`下新建 `ApiController` 类，代码如下：

```java
package com.lou.springboot.controller;

import com.lou.springboot.common.Result;
import com.lou.springboot.common.ResultGenerator;
import com.lou.springboot.entity.User;
import org.springframework.stereotype.Controller;
import org.springframework.util.StringUtils;
import org.springframework.web.bind.annotation.*;
import java.util.*;
/**
 * @author 13
 * @qq交流群 796794009
 * @email 2449207463@qq.com
 * @link http://13blog.site
 */
@Controller
@RequestMapping("/api")
public class ApiController {
    static Map<Integer, User> usersMap = Collections.synchronizedMap(new HashMap<Integer, User>());

    // 初始化 usersMap
    static {
        User user = new User();
        user.setId(2);
        user.setName("user1");
        user.setPassword("123456");
        User user2 = new User();
        user2.setId(5);
        user2.setName("13-5");
        user2.setPassword("4");
        User user3 = new User();
        user3.setId(6);
        user3.setName("12");
        user3.setPassword("123");
        usersMap.put(2, user);
        usersMap.put(5, user2);
        usersMap.put(6, user3);
    }

    // 查询一条记录
    @RequestMapping(value = "/users/{id}", method = RequestMethod.GET)
    @ResponseBody
    public Result<User> getOne(@PathVariable("id") Integer id) {
        if (id == null || id < 1) {
            return ResultGenerator.genFailResult("缺少参数");
        }
        User user = usersMap.get(id);
        if (user == null) {
            return ResultGenerator.genFailResult("无此数据");
        }
        return ResultGenerator.genSuccessResult(user);
    }

    // 查询所有记录
    @RequestMapping(value = "/users", method = RequestMethod.GET)
    @ResponseBody
    public Result<List<User>> queryAll() {
        List<User> users = new ArrayList<User>(usersMap.values());
        return ResultGenerator.genSuccessResult(users);
    }

    // 新增一条记录
    @RequestMapping(value = "/users", method = RequestMethod.POST)
    @ResponseBody
    public Result<Boolean> insert(@RequestBody User user) {
        // 参数验证
        if (StringUtils.isEmpty(user.getId()) || StringUtils.isEmpty(user.getName()) || StringUtils.isEmpty(user.getPassword())) {
            return ResultGenerator.genFailResult("缺少参数");
        }
        if (usersMap.containsKey(user.getId())) {
            return ResultGenerator.genFailResult("重复的id字段");
        }
        usersMap.put(user.getId(), user);
        return ResultGenerator.genSuccessResult(true);
    }

    // 修改一条记录
    @RequestMapping(value = "/users", method = RequestMethod.PUT)
    @ResponseBody
    public Result<Boolean> update(@RequestBody User tempUser) {
        //参数验证
        if (tempUser.getId() == null || tempUser.getId() < 1 || StringUtils.isEmpty(tempUser.getName()) || StringUtils.isEmpty(tempUser.getPassword())) {
            return ResultGenerator.genFailResult("缺少参数");
        }
        //实体验证，不存在则不继续修改操作
        User user = usersMap.get(tempUser.getId());
        if (user == null) {
            return ResultGenerator.genFailResult("参数异常");
        }
        user.setName(tempUser.getName());
        user.setPassword(tempUser.getPassword());
        usersMap.put(tempUser.getId(), tempUser);
        return ResultGenerator.genSuccessResult(true);
    }

    // 删除一条记录
    @RequestMapping(value = "/users/{id}", method = RequestMethod.DELETE)
    @ResponseBody
    public Result<Boolean> delete(@PathVariable("id") Integer id) {
        if (id == null || id < 1) {
            return ResultGenerator.genFailResult("缺少参数");
        }
        usersMap.remove(id);
        return ResultGenerator.genSuccessResult(true);
    }
}
```

**根据前端不同的资源请求，我们按照前文中 HTTP 动词的要求对接口的请求类型进行设置**，<u>用户数据查询方法使用 GET 请求，用户添加方法 使用 POST 请求，对应的修改和删除操作使用 PUT 和 DELETE 请求</u>，**同时对于 api 的请求路径也按照设计规范进行设置，虽然有些映射路径相同，但是会根据请求方法进行区分**。

比如：同样是 /users 路径，如果请求方法为 POST 则表示添加资源会调用 insert() 方法，而请求方法为 PUT 时则表示修改资源会调用 update() 方法，还有 /users/{id} 路径，会根据 GET 请求方式和 DELETE 请求方式进行区分表示获取单个资源和删除单个资源。

同时，每一个返回结果我们都统一使用 Result 类进行包装之后再返回给前端，并使用 `@ResponseBody` 注解将其转换为 json 格式。



### 前端页面和 js 方法实现

接口定义完成后就可以进行前端页面和接口调用逻辑的实现了，新建 api-test.html（注：代码位于`resources/static`），代码如下：

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <title>lou.SpringBoot | api 请求测试</title>
  </head>
  <body class="hold-transition login-page">
    <div style="width:720px;margin:7% auto">
      <div class="content">
        <div class="container-fluid">
          <div class="row">
            <div class="col-lg-6">
              <hr />
              <div class="card">
                <div class="card-header">
                  <h5 class="m-0">详情查询接口测试</h5>
                </div>
                <div class="card-body">
                  <input
                    id="queryId"
                    type="number"
                    placeholder="请输入id字段"
                  />
                  <h6 class="card-title">查询接口返回数据如下：</h6>
                  <p class="card-text" id="result0"></p>
                  <a href="#" class="btn btn-primary" onclick="requestQuery()"
                    >发送详情查询请求</a
                  >
                </div>
              </div>
              <br />
              <hr />
              <div class="card">
                <div class="card-header">
                  <h5 class="m-0">列表查询接口测试</h5>
                </div>
                <div class="card-body">
                  <h6 class="card-title">查询接口返回数据如下：</h6>
                  <p class="card-text" id="result1"></p>
                  <a
                    href="#"
                    class="btn btn-primary"
                    onclick="requestQueryList()"
                    >发送列表查询请求</a
                  >
                </div>
              </div>
              <br />
              <hr />
              <div class="card">
                <div class="card-header">
                  <h5 class="m-0">添加接口测试</h5>
                </div>
                <div class="card-body">
                  <input id="addId" type="number" placeholder="请输入id字段" />
                  <input
                    id="addName"
                    type="text"
                    placeholder="请输入name字段"
                  />
                  <input
                    id="addPassword"
                    type="text"
                    placeholder="请输入password字段"
                  />
                  <h6 class="card-title">添加接口返回数据如下：</h6>
                  <p class="card-text" id="result2"></p>
                  <a href="#" class="btn btn-primary" onclick="requestAdd()"
                    >发送添加请求</a
                  >
                </div>
              </div>
              <br />
              <hr />
              <div class="card">
                <div class="card-header">
                  <h5 class="m-0">修改接口测试</h5>
                </div>
                <div class="card-body">
                  <input
                    id="updateId"
                    type="number"
                    placeholder="请输入id字段"
                  />
                  <input
                    id="updateName"
                    type="text"
                    placeholder="请输入name字段"
                  />
                  <input
                    id="updatePassword"
                    type="text"
                    placeholder="请输入password字段"
                  />
                  <h6 class="card-title">修改接口返回数据如下：</h6>
                  <p class="card-text" id="result3"></p>
                  <a href="#" class="btn btn-primary" onclick="requestUpdate()"
                    >发送修改请求</a
                  >
                </div>
              </div>
              <br />
              <hr />
              <div class="card">
                <div class="card-header">
                  <h5 class="m-0">删除接口测试</h5>
                </div>
                <div class="card-body">
                  <input
                    id="deleteId"
                    type="number"
                    placeholder="请输入id字段"
                  />
                  <h6 class="card-title">删除接口返回数据如下：</h6>
                  <p class="card-text" id="result4"></p>
                  <a href="#" class="btn btn-primary" onclick="requestDelete()"
                    >发送删除请求</a
                  >
                </div>
              </div>
              <hr />
            </div>
          </div>
        </div>
      </div>
    </div>
  </body>
</html>
```

本功能主要包括对于 tb_user 表增删改查功能的调用和结果显示，每个接口测试模块包括信息录入的 input 框和发送请求按钮以及结果显示的 div，点击不同的按钮分别触发不同的 js 方法，我们计划在 js 方法中使用 Ajax 对后端发送不同的请求，实现如下：

```html
<!-- jQuery -->
<script src="https://cdn.staticfile.org/jquery/1.12.0/jquery.min.js"></script>
<script type="text/javascript">
  function requestQuery() {
    var id = $('#queryId').val();
    if (typeof id == 'undefined' || id == null || id == '' || id < 0) {
      return false;
    }
    $.ajax({
      type: 'GET', //方法类型
      dataType: 'json', //预期服务器返回的数据类型
      url: '/api/users/' + id,
      contentType: 'application/json; charset=utf-8',
      success: function (result) {
        $('#result0').html(JSON.stringify(result));
      },
      error: function () {
        $('#result0').html('接口异常，请联系管理员！');
      },
    });
  }
  function requestQueryList() {
    $.ajax({
      type: 'GET', //方法类型
      dataType: 'json', //预期服务器返回的数据类型
      url: '/api/users',
      contentType: 'application/json; charset=utf-8',
      success: function (result) {
        $('#result1').html(JSON.stringify(result));
      },
      error: function () {
        $('#result1').html('接口异常，请联系管理员！');
      },
    });
  }
  function requestAdd() {
    var id = $('#addId').val();
    var name = $('#addName').val();
    var password = $('#addPassword').val();
    var data = { id: id, name: name, password: password };
    $.ajax({
      type: 'POST', //方法类型
      dataType: 'json', //预期服务器返回的数据类型
      url: '/api/users',
      contentType: 'application/json; charset=utf-8',
      data: JSON.stringify(data),
      success: function (result) {
        $('#result2').html(JSON.stringify(result));
      },
      error: function () {
        $('#result2').html('接口异常，请联系管理员！');
      },
    });
  }
  function requestUpdate() {
    var id = $('#updateId').val();
    var name = $('#updateName').val();
    var password = $('#updatePassword').val();
    var data = { id: id, name: name, password: password };
    $.ajax({
      type: 'PUT', //方法类型
      dataType: 'json', //预期服务器返回的数据类型
      url: '/api/users',
      contentType: 'application/json; charset=utf-8',
      data: JSON.stringify(data),
      success: function (result) {
        $('#result3').html(JSON.stringify(result));
      },
      error: function () {
        $('#result3').html('接口异常，请联系管理员！');
      },
    });
  }
  function requestDelete() {
    var id = $('#deleteId').val();
    if (typeof id == 'undefined' || id == null || id == '' || id < 0) {
      return false;
    }
    $.ajax({
      type: 'DELETE', //方法类型
      dataType: 'json', //预期服务器返回的数据类型
      url: '/api/users/' + id,
      contentType: 'application/json; charset=utf-8',
      success: function (result) {
        $('#result4').html(JSON.stringify(result));
      },
      error: function () {
        $('#result4').html('接口异常，请联系管理员！');
      },
    });
  }
</script>
```

每个请求按钮点击后会触发不同的 js 方法，以修改请求为例，该按钮点击后会触发 `requestUpdate()` 方法，该方法中首先会获取用户输入的数据，之后将其封装到 data 中，同时设置请求的 url 为 /api/users，由于是修改用户数据，因此将请求方法设置为 PUT 方法，之后向后端发送请求，而后端代码在前文中已经介绍，请求地址为 /api/users 且请求方法为 PUT 时会调用修改方法将用户数据进行修改。

删除请求也是如此，首先用户输入一个需要删除的 id，之后根据该 id 将其拼接到 url 中，同时设置请求的 url 为 /api/users/{id}，由于是删除用户数据，因此将请求方法设置为 DELETE 方法，之后向后端发送请求，后端在收到请求后会根据请求方法和请求地址进行映射匹配，可以通过后端代码得知，该请求最终会被控制器中的 `delete()` 方法处理，其它的调用流程与此类似，可以参考着进行理解。

> `测试项目：spring-boot-restful-api`





## SpringBoot博客系统项目学习

### SpringBoot博客系统项目开发之分页功能实现

#### 分页的作用

不仅仅是常见，分页功能在一个系统中也是不可缺少的，分页功能的作用如下：

- 减少系统资源的消耗，数据查询出来后是放在内存里的，如果在数据量很大的情况下一次性将所有内容都查询出来，会占用过多的内存，通过分页可以减少这种消耗；
- 提高性能，应用与数据库间通过网络传输数据，一次传输 10 条数据结果集与一次传输 20000 条数据结果集肯定是传输 10 条消耗更少的网络资源；
- 提升访问速度，浏览器与应用间的传输也是通过网络，返回 10 条数据明显那比返回 20000 条数据速度更快，因为数据包的大小有差别；
- 符合用户习惯，比如搜索结果或者商品展示，通常用户可能只看最近前 30 条，将所有数据都查询出来比较浪费；
- 基于展现层面的考虑，由于设备屏幕的大小比较固定，一个屏幕能够展示的信息并不是特别多，如果一次展现太多的数据，不管是排版还是页面美观度都有影响，一个屏幕的范围就是那么大，展示信息条数有限。

分页功能的使用可以提升系统性能，也比较符合用户习惯，符合页面设计，这也是为什么大部分系统都会有分页功能。



#### JqGrid 分页插件介绍

JqGrid 是一个用来显示网格数据的 jQuery 插件，通过使用 jqGrid 可以轻松实现前端页面与后台数据的 Ajax 异步通信并实现分页功能，特点如下：

- 兼容目前所有流行的 web 浏览器；
- 完善强大的分页功能；
- 支持多种数据格式解析，XML、JSON、数组等形式；
- 提供丰富的选项配置及方法事件接口；
- 支持表格排序，支持拖动列、隐藏列；
- 支持滚动加载数据；
- 开源免费

[JqGrid in GitHub](https://github.com/tonytomov/jqGrid/tree/master)

[下载地址](https://github.com/tonytomov/jqGrid/releases)

本教程选择的版本为 5.3.0，将代码压缩包解压后可以看到 JqGrid 正式包的目录结构如下：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid441493labid8432timestamp1550026852832.png)

使用 JqGrid 时必要的文件如下：

```
## js文件
jquery.jqGrid.js
grid.locale-cn.js
jquery.jqGrid.js

## 样式文件
ui.jqgrid-bootstrap-ui.css
ui.jqgrid-bootstrap.css
ui.jqgrid.css
```

主要是 js 文件和 css 样式文件，如果想使用 JqGrid 其他特性的话对应的引入其 js 文件即可。



#### 整合过程

1. 首先在 html 文件中引入 JqGrid 所需文件：

```html
<link href="plugins/jqgrid-5.3.0/ui.jqgrid-bootstrap4.css" rel="stylesheet" />
<!-- JqGrid依赖jquery，因此需要先引入jquery.min.js文件 -->
<script src="plugins/jquery/jquery.min.js"></script>

<script src="plugins/jqgrid-5.3.0/grid.locale-cn.js"></script>
<script src="plugins/jqgrid-5.3.0/jquery.jqGrid.min.js"></script>
```

1. 在页面中需要展示分页数据的区域添加如下代码，用于 JqGrid 初始化：

```html
<!-- JqGrid必要DOM,用于创建表格展示列表数据 -->
<table id="jqGrid" class="table table-bordered"></table>
<!-- JqGrid必要DOM,分页信息区域 -->
<div id="jqGridPager"></div>
```

1. 调用 JqGrid 分页插件的 jqGrid() 方法渲染分页展示区域，代码如下：

```js
$('#jqGrid').jqGrid({
  url: 'users/list', // 请求后台json数据的url
  datatype: 'json', // 后台返回的数据格式
  colModel: [
    // 列表信息：表头 宽度 是否显示 渲染参数 等属性
    {
      label: 'id',
      name: 'id',
      index: 'id',
      width: 50,
      hidden: true,
      key: true,
    },
    {
      label: '登录名',
      name: 'userName',
      index: 'userName',
      sortable: false,
      width: 80,
    },
    {
      label: '添加时间',
      name: 'createTime',
      index: 'createTime',
      sortable: false,
      width: 80,
    },
  ],
  height: 485, // 表格高度  可自行调节
  rowNum: 10, // 默认一页显示多少条数据 可自行调节
  rowList: [10, 30, 50], // 翻页控制条中 每页显示记录数可选集合
  styleUI: 'Bootstrap', // 主题 这里选用的是Bootstrap主题
  loadtext: '信息读取中...', // 数据加载时显示的提示信息
  rownumbers: true, // 是否显示行号，默认值是false，不显示
  rownumWidth: 35, // 行号列的宽度
  autowidth: true, // 宽度自适应
  multiselect: true, // 是否可以多选
  pager: '#jqGridPager', // 分页信息DOM
  jsonReader: {
    root: 'data.list', //数据列表模型
    page: 'data.currPage', //数据页码
    total: 'data.totalPage', //数据总页码
    records: 'data.totalCount', //数据总记录数
  },
  // 向后台请求的参数
  prmNames: {
    page: 'page',
    rows: 'limit',
    order: 'order',
  },
  // 数据加载完成并且DOM创建完毕之后的回调函数
  gridComplete: function () {
    //隐藏grid底部滚动条
    $('#jqGrid').closest('.ui-jqgrid-bdiv').css({ 'overflow-x': 'hidden' });
  },
});
```

该代码的含义为：**在页面加载时，调用 JqGrid 的初始化方法，将页面中 id 为 jqGrid 的 DOM 渲染为分页表格，并向后端发送请求，之后按照后端返回的 json 数据填充表格以及表格下方的分页按钮，第一页、下一页、最后一页等等逻辑都有 JqGrid 内部实现了，我们只需要将它初始化时所需要的几个数据设置好即可，<u>因此我们需要将返回格式设置为 PageResult 类所封装的数据类型</u>。**

由于 user.html 文件中引入了 user.js 文件，所以在页面加载完成后会进行数据列表的渲染及分页插件的渲染，用户可以直接使用翻页功能了，本来这些功能需要我们自行实现，但是使用 JqGrid 后这些都不需要我们再去做逻辑实现，只需要调用其分页方法并将所需的参数设置好即可，十分方便。



##### 分页数据格式详解

在 JqGrid 整合中有如下代码：

```json
jsonReader: {
  root: "data.list", //数据列表模型
  page: "data.currPage", //当前页码
  total: "data.totalPage", //数据总页码
  records: "data.totalCount" //数据总记录数
  }
```

这里定义的是 jsonReader 对象如何对后端返回的 json 数据进行解析，比如数据列表为何读取 "data.list"，当前页码为何读取 "data.currPage"，这些都是由后端返回的数据格式所决定的，后端响应结果的数据格式定义在`com.lou.springboot.common.Result` 类中：

```java
public class Result<T> implements Serializable {
    //响应码 200为成功
    private int resultCode;
    //响应msg
    private String message;
    //返回数据
    private T data;
}
```

即所有的数据都会被设置到 data 属性中，分页结果集的数据格式定义如下（注：完整代码位于`com.lou.springboot.util.PageResult`）：

```java
public class PageResult implements Serializable {
    //总记录数
    private int totalCount;
    //每页记录数
    private int pageSize;
    //总页数
    private int totalPage;
    //当前页数
    private int currPage;
    //列表数据
    private List<?> list;
}
```

由于 JqGrid 分页插件在实现分页功能时必须以下四个参数：**当前页的所有数据列表、当前页的页码、总页码、总记录数量**，因此我们封装了 PageResult 对象，并将其放入 Result 返回结果的 data 属性中，之后在 JqGrid 读取时直接读取对应的参数即可，这就是前后端进行数据交互时的格式定义



### SpringBoot博客系统项目开发之登录与验证码功能

#### 验证码介绍

谈起验证码这个话题，相信大家都应该很熟悉，在日常上网的时候经常会看到验证码的逻辑设计，比如登陆账号、论坛发帖、购买商品时网站都会要求用户在实际操作之前去输入验证码，验证码的生成规则或者展现形式也各不相同，但是这个设计确实已经存在于各大网站中，以下是百度百科中对它的定义：

> 验证码（CAPTCHA）是“Completely Automated Public Turing test to tell Computers and Humans Apart”（全自动区分计算机和人类的图灵测试）的缩写，是一种区分用户是计算机还是人的公共全自动程序。可以防止：恶意破解密码、刷票、论坛灌水，有效防止某个黑客对某一个特定注册用户用特定程序暴力破解方式进行不断的登陆尝试，实际上用验证码是现在很多网站通行的方式，我们利用比较简易的方式实现了这个功能。这个问题可以由计算机生成并评判，但是必须只有人类才能解答。由于计算机无法解答 CAPTCHA 的问题，所以回答出问题的用户就可以被认为是人类。



#### 验证码的输入验证

验证码的显示完成后，我们接下来要做的就是对用户输入的验证码进行比对和验证，因为一般的做法就是后端生成后会对当前生成的验证码进行保存（可能是 session 中、或者缓存中、或者数据库中），之后显示到前端页面，用户在看到验证码之后在页面对应的输入框中填写验证码，之后才向后端发送请求，而后端再接到请求后会对用户输入的验证码进行验证，如果不对的话则不会进行后续操作，接下来我们来简单的实现一下这个流程。

##### 后端处理

在 KaptchaController 类中新增 `verify` 方法，代码如下：

```java
    @GetMapping("/verify")
    @ResponseBody
    public String verify(@RequestParam("code") String code, HttpSession session) {
        if (StringUtils.isEmpty(code)) {
            return "验证码不能为空";
        }
        String kaptchaCode = session.getAttribute("verifyCode") + "";
        if (StringUtils.isEmpty(kaptchaCode) || !code.equals(kaptchaCode)) {
            return "验证码错误";
        }
        return "验证成功";
    }
```

该方法所拦截处理的路径为 /verify，请求参数为 code，即用户输入的验证码，在进行基本的非空验证后，与之前保存在 session 中的 verifyCode 值进行比较，不同则返回验证码错误，相同则返回验证成功。

##### 前端处理

新建 verify.html，该页面中显示验证码，同时有可以供用户输入验证码的输入框以及提交按钮，代码如下：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>验证码测试</title>
  </head>
  <body>
    <img src="/kaptcha" onclick="this.src='/kaptcha?d='+new Date()*1" />
    <input type="text" maxlength="5" id="code" placeholder="请输入验证码" />
    <button id="verify">验证</button>
  </body>
  <script src="jquery.js"></script>
  <script type="text/javascript">
    $(function () {
      $('#verify').click(function () {
        var code = $('#code').val();
        $.ajax({
          type: 'GET', //方法类型
          url: '/verify?code=' + code,
          success: function (result) {
            alert(result);
          },
          error: function () {
            alert('请求失败');
          },
        });
      });
    });
  </script>
</html>
```

用户在输入框中输入验证码后可以点击“验证”按钮，点击事件触发后执行 js 方法，该方法会获取到用户输入的验证码的值，并将其作为请求参数，之后进行 Ajax 请求，请求后会在弹框中显示后端返回的处理结果。



#### 登陆拦截器功能

在前一个实验中我们实现了管理员的登陆功能，该功能已经完成，但是身份认证的整个流程并没有完善，该流程中应该包括**登陆功能、身份认证、访问拦截、退出功能**，我们仅仅完成了第一步，因此本实验将会对该流程进行完善，将接下来的功能点完成。

举个简单点的例子，我们登陆的目的是为了能够访问后台页面（比如前一个实验中的首页，路径为 /admin/index），但是在运行前一个实验的代码时，即使我们不进行登陆操作也可以访问该页面，同学们可以自行实验，也就是说我们并没有在用户访问时进行身份认证和访问拦截，所以我们首先把访问拦截的功能给完善了，大家也都知道，实现这个功能我们通常的做法就是使用拦截器，那么接下来我们就来实现这个拦截器吧！



#### 拦截器介绍

定义一个 Interceptor 非常简单，方式也有几种，这里简单列举两种：

- 新建类要实现 Spring 的 HandlerInterceptor 接口
- 新建类继承实现了 HandlerInterceptor 接口的实现类，例如已经提供的实现了 HandlerInterceptor 接口的抽象类 HandlerInterceptorAdapter

HandlerInterceptor 方法介绍：

```java
    boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
            throws Exception;

    void postHandle(
            HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception;

    void afterCompletion(
            HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception;
```

- **preHandle**：在业务处理器处理请求之前被调用。预处理，可以进行编码、安全控制、权限校验等处理；
- **postHandle**：在业务处理器处理请求执行完成后，生成视图之前执行。
- **afterCompletion**：在 DispatcherServlet 完全处理完请求后被调用，可用于清理资源等，返回处理（已经渲染了页面）；



#### 定义拦截器

新建 interceptor 包，在包中新建 AdminLoginInterceptor 类，该类需要实现 HandlerInterceptor 接口，代码如下：

```java
package com.site.blog.my.core.interceptor;
import org.springframework.stereotype.Component;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * 后台系统身份验证拦截器
 */
@Component
public class AdminLoginInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object o) throws Exception {
        String uri = request.getRequestURI();
        if (uri.startsWith("/admin") && null == request.getSession().getAttribute("loginUser")) {
            request.getSession().setAttribute("errorMsg", "请登陆");
            response.sendRedirect(request.getContextPath() + "/admin/login");
            return false;
        } else {
            request.getSession().removeAttribute("errorMsg");
            return true;
        }
    }
    @Override
    public void postHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, ModelAndView modelAndView) throws Exception {
    }
    @Override
    public void afterCompletion(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) throws Exception {
    }
}
```

我们只需要完善 `preHandle` 方法即可，同时在类声明上方添加 `@Component` 注解使其注册到 IOC 容器中。通过上面代码可以看出，在请求的预处理过程中读取当前 session 中是否存在 loginUser 对象，如果不存在则返回 false 并跳转至登录页面，如果已经存在则返回 true，继续做后续处理流程。

#### 配置拦截器

在实现拦截器的相关方法之后，我们需要对该拦截器进行配置以使其生效，在 Spring Boot 1.x 版本中我们通常会继承 WebMvcConfigurerAdapter 类，但是在 Spring Boot 2.x 版本中，WebMvcConfigurerAdapter 被弃用，虽然继承 WebMvcConfigurerAdapter 这个类虽然有此便利，但在 Spring 5.0 里面已经被弃用了，官方文档也说了，WebMvcConfigurer 接口现在已经有了默认的空白方法，所以在 Spring Boot 2.x 版本下更好的做法还是实现  WebMvcConfigurer 接口。

新建 config 包，之后新建 MyBlogWebMvcConfigurer 类并实现 WebMvcConfigurer 接口，代码如下：

```java
package com.site.blog.my.core.config;

import com.site.blog.my.core.interceptor.AdminLoginInterceptor;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class MyBlogWebMvcConfigurer implements WebMvcConfigurer {

    @Autowired
    private AdminLoginInterceptor adminLoginInterceptor;

    public void addInterceptors(InterceptorRegistry registry) {
        // 添加一个拦截器，拦截以/admin为前缀的url路径
        registry.addInterceptor(adminLoginInterceptor).addPathPatterns("/admin/**").excludePathPatterns("/admin/login").excludePathPatterns("/admin/dist/**").excludePathPatterns("/admin/plugins/**");
    }
}
```

在该配置类中，我们添加刚刚新增的 AdminLoginInterceptor 登录拦截器，并对该拦截器所拦截的路径进行配置，由于后端管理系统的所有请求路径都以 /admin 开头，所以拦截的路径为 /admin/** ，但是登陆页面以及部分静态资源文件也是以 /admin 开头，所以需要将这些路径排除，配置如上。



### SpringBoot博客系统项目开发之标签功能实现

#### 标签模块简介

标签是一种更为灵活、更有趣的分类方式，在书写博客时可以为每篇文章添加一个或多个标签，在博客系统中，文章的标签设计被广泛应用，我们可以看到大部分的博客网站中都会有标签设计，因此，在设计 personal-blog 这个项目时，也将标签运用了进来。

标签最明显的作用有如下两点：

- 一是传统意义上分类的作用，类似分类名称
- 二是对文章内容进行一定程度的描述，类似于关键词

虽然与分类设计类似，但是标签和分类还有一些细区别：

- 同一篇文章标签可以用多个，但通常只能属于一个分类
- 标签一般是在写作完成后，根据文章内容自行添加的内容
- 标签可以把文章中重点词语提炼出来，有关键词的意义，但是分类没有
- 标签通常更为主观，其内容相较于分类来说更加具体一些

与分类的功能和设计思想类似，但是又有一定的不同，标签可以算是分类的细化版本，同时，一篇博客的分类最好只有一个，但是在设计的时候，一篇博客的标签是可以有多个的，标签设计的介绍就到这里，接下来是功能开发的讲解

#### 表结构设计及 Mapper 文件自动生成

**标签与文章的关系**

通过前文中标签设计的介绍我们也大致的了解了标签与文章的关系，一篇文章可以有多个标签字段，一个标签字段也可以被标注在多个文章中，这个情况与前一个实验中的分类设计是有一些差别的，标签实体与文章实体的关系是多对多的关系，因此在表结构设计时不仅仅需要标签实体和文章实体的字段映射，还需要存储二者之间的关系数据，本系统采用的方式是新增一张关系表来维护二者多对多的关联关系。



#### 表结构设计

标签表以及标签文章关系表的 SQL 设计如下，直接执行如下 SQL 语句即可：

```sql
USE `my_blog_db`;

DROP TABLE IF EXISTS `tb_blog_tag`;

CREATE TABLE `tb_blog_tag` (
  `tag_id` int(11) NOT NULL AUTO_INCREMENT COMMENT '标签表主键id',
  `tag_name` varchar(100) NOT NULL COMMENT '标签名称',
  `is_deleted` tinyint(4) NOT NULL DEFAULT '0' COMMENT '是否删除 0=否 1=是',
  `create_time` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  PRIMARY KEY (`tag_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

DROP TABLE IF EXISTS `tb_blog_tag_relation`;

CREATE TABLE `tb_blog_tag_relation` (
  `relation_id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT '关系表id',
  `blog_id` bigint(20) NOT NULL COMMENT '博客id',
  `tag_id` int(11) NOT NULL COMMENT '标签id',
  `create_time` datetime DEFAULT CURRENT_TIMESTAMP COMMENT '添加时间',
  PRIMARY KEY (`relation_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

两张表的字段以及每个字段对应的含义都在上面的 SQL 中有介绍，大家可以对照 SQL 进行理解，在关系表中有一个 blog_id 字段，是文章表的主键 id，虽然暂时还没有介绍文章模块，但是大家应该能够理解关系表存放的字段含义，这张表存储的就是标签记录对应的文章记录，以多对多的方式进行记录的，把表结构导入到数据库中即可，接下来我们进行编码工作。





### SpringBoot博客系统项目开发之文章编辑功能

#### **什么是富文本编辑器？**

> 富文本编辑器，是一种可内嵌于浏览器，所见即所得的文本编辑器。 富文本编辑器不同于文本编辑器(如 textarea 标签、input 标签)，也可以叫做图文编辑器，在富文本编辑器里可以编辑非常丰富的内容，如文字、图片、表情、代码……应有尽有，满足你的大部分需求。 像一些新闻排版，基本是以图文排版为主，而淘宝京东这些电商的商品详情页，基本都是多张已经排版好的设计图拼接而来的，富文本编辑器可以很完美的支持者两种需求。

目前的富文本编辑器主要有 markdown 版本和非 markdown 版本的编辑器，一般企业开发中使用非 markdown 版本比较多，常见的有 UEditor 和 KindEditor 等，因为运营人员可能不太懂 markdown 语法，而博客文章的编辑通常是使用 markdown 编辑器（即 md 编辑器），因为这部分人员掌握 markdown 语法也很快，所以大部分博客网站都会默认使用 markdown 编辑器作为用户的文章编辑器，比如咱们实验楼的编辑器也支持 markdown 语法。

#### 为什么要使用富文本编辑器

以下是使用富文本编辑器的原因，也是富文本编辑器的优点：

- 需求变更导致，业务方提出的编辑需求越来越复杂
- 编辑的内容变得越来越复杂、越来越丰富
- 比起编辑 html，富文本编辑器更灵活
- 富文本编辑器功能丰富，满足大部分需求

#### Editor.md 编辑器介绍

![图片描述](https://doc.shiyanlou.com/courses/uid987099-20190729-1564379469760)

本次实战项目中所选用的富文本编辑器为 Editor.md，这一节将会详细介绍它，即使通过让大家通过文章就可以感受到它的强大，Editor.md 是一款开源的、可嵌入的 Markdown 在线编辑器（组件），基于 CodeMirror、jQuery 和 Marked 构建。

**主要特性**

- 支持通用 Markdown / CommonMark 和 GFM (GitHub Flavored Markdown) 风格的语法，也可变身为代码编辑器；
- 支持实时预览、图片（跨域）上传、预格式文本/代码/表格插入、代码折叠、跳转到行、搜索替换、只读模式、自定义样式主题和多语言语法高亮等功能；
- 支持 ToC（Table of Contents）、Emoji 表情、Task lists、@链接等 Markdown 扩展语法；
- 支持 TeX 科学公式（基于 KaTeX）、流程图 Flowchart 和 时序图 Sequence Diagram;
- 支持识别和解析 HTML 标签，并且支持自定义过滤标签及属性解析，具有可靠的安全性和几乎无限的扩展性；
- 支持 AMD / CMD 模块化加载（支持 Require.js & Sea.js），并且支持自定义扩展插件；
- 兼容主流的浏览器（IE8+）和 Zepto.js，且支持 iPad 等平板设备；

[Editor.md 主页](http://editor.md.ipandao.com/)

[Editor.md in GitHub](https://github.com/pandao/editor.md)

Editor.md 编辑器的功能很多，那么在博客编辑时最常用的功能可以到它的主页去了解，官网上也有很多的实用案例可以让你快速上手，有的同学可能不太熟悉 markdown 语法，也可以针对性的去学习一下，这里由于篇幅所限就不再介绍了。



#### 整合思路：？

前端html页面(引入cs，js)->js文件->controller代码->添加拦截器



## 开发拓展知识

### Java日志框架SLF4J和log4j以及logback的联系和区别

https://www.cnblogs.com/hanszhao/p/9754419.htm

logback与log4j系列学习教程：http://www.51gjie.com/javaweb/1107.html



### logback简单配置学习

```xml
<configuration scan="true" scanPeriod="60 seconds" debug="false">
    <property name="appName" value="heron"/>

    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss} [%-5level] %logger{36} [%file:%line] - %msg%n</pattern>
        </encoder>
    </appender>


    <root level="INFO">
        <appender-ref ref="STDOUT"/>
    </root>
</configuration>
```

参考:https://www.cnblogs.com/gavincoder/p/10091757.html

https://segmentfault.com/a/1190000037513451



### StringUtils.equals和String.equals

这种情况下二者是一样的,
String a = "";
String b = "";
System.out.println(StringUtils.equals(a,b));//true
System.out.println(a.equals(b));//true

这种情况下是会出问题的:
String a = null; 
String b = null;
System.out.println(StringUtils.equals(a,b));//true
System.out.println(a.equals(b));//报空指针,因为a为null

即StringUtils可以接受空指针的情况。





## Bug集合

### [解决问题 The Tomcat connector configured to listen on port 8080 failed to start.](https://blog.csdn.net/q343509740/article/details/80821409)



## 简单命令行操作

cd projectName

通过 Maven 插件的方式启动 Spring Boot 项目：mvn spring-boot:run

解压命令：unzip lou-springboot.zip





## 补习

MySQL--Group by分组与count计数（进阶)

https://blog.csdn.net/L1542334210/article/details/101561608?utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.baidujs&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.baidujs

