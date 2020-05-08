## Spring相关

#### 1.Spring概念

Spring Framework是一个轻量级的IOC和AOP容器框架．为Java应用程序提供基础性服务的一套框架．目的是简化企业应用程序的开发，使得开发者只需要关心业务需求．常见的配置有三种：基于xml配置，基于注解配置，基于Java配置．

Spring准确来说是一个生态体系，是集大成者，包括Spring Framework,Spring Boot,Spring Cloud等．

主要模块：

- Spring Core:核心类库，提供IOC服务
- Spring Context:提供框架式的Bean访问服务，以及企业级功能
- Spring AOP:AOP服务
- Spring DAO:对JDBC抽象，简化数据访问异常的处理
- Spring ORM:对现有ORM框架的支持
- Spring Web:提供基本的面向Web综合特性
- Spring MVC:提供面向web应用的Model-View-Controller实现

#### 2.Spring Bean和java bean比较区别

**比较：**

１．规范：Spring容器对Bean 没有特殊要求，不像JavaBean 一样遵循一些规范（为每个属性提供相应的setter 和 getter 方法），不过对于设值注入的Bean,一定要提供setter 方法。

２．作用：Spring 中的Bean 是 java 实例，java组件，它的作用几乎无所不包，任何应用组件都被称为Bean，而传统的Java应用中的JavaBean通常作为DTO（数据传输对象），来封装值对象，在各层之间传递数据。

**Spring 的Bean继承和java继承的区别**

1. Spring中子Bean和父Bean可以是不同的类型，而java中的继承子类只是父类的一种特殊类型。
2. Spring中Bean的继承是实例之间的关系，主要表现为参数值的延续，而java中的继承是类之间的关系，主要表现为属性，方法的延续。
3. Spring中子Bean不可作父Bean使用，不具备多态性，java中的子类实例完全可以做父类的实例使用

#### 3.说一下IOC和AOP?

**IOC**:

控制反转，也叫做依赖注入．具体来说：当某角色需要另外一个角色协助时，在传统的程序设计过程中，通常是调用者创建被调用者的实例．但在Spring中创建被调用者的工作不由调用者完成，而将其交给Spring完成．

容器初始化过程：

- 资源定位：找到bean对应的文件
- 装载：将找到的资源载入到BeanDefinition(IOC容器中保存bean信息的一种数据结构)
- 注册：将BeanDefinition注册到容器中．将前面的BeanDefinition保存到HashMap．

注入方式：

- 构造器：
  - 在构造期间完成一个完整的、合法的对象。
  - 所有依赖关系在构造函数中集中呈现。
  - 依赖关系在构造时由容器一次性设定，组件被创建之后一直处于相对“不变”的稳定状态。
  - 只有组件的创建者关心其内部依赖关系，对调用者而言，该依赖关系处于“黑盒”之中。
- 接口注入：接口注入模式因为具备侵入性，它要求组件必须与特定的接口相关联，因此并不被看好，实际使用有限
- set注入：
  - 对于习惯了传统 javabean 开发的程序员，通过 setter 方法设定依赖关系更加直观。
  - 如果依赖关系较为复杂，那么构造子注入模式的构造函数也会相当庞大，而此时设值注入模式则更为简洁。
  - 如果用到了第三方类库，可能要求我们的组件提供一个默认的构造函数，此时构造子注入模式也不适用。

**AOP**:

面向切面编程，用于将与业务无关，但却对多个对象产生影响的公共行为和逻辑，抽取并封装成一个可重用的模块，该模块称为切面．可以减少系统中重复代码，降低模块之间的耦合度，提高系统可维护性．可用于权限认证、日志、事务处理。

**术语：**

- 连接点(Joint Point)：指程序执行的某个特定位置．如类初始化之前，类初始化后，方法调用前/后，方法抛出异常后．
- 切点(Pointcut)：表示一组Joint Point,它定义了Advice将要发生的地方
- 增强(Advice):定义将在切点里将要执行的操作
- 目标对象(Target):织入adcive的目标对象
- 织入(Weaving):将切面和其它对象连接起来，并创建增强对象的过程
- 切面(Aspect)：切点和增强结合起来就是切面，类似于一个类，但是更多的是对象之间的横向关系

Spring AOP使用**动态代理技术**在运行期间织入增强的代码.Spring AOP使用了两种代理机制：一种基于JDK的动态代理；一种基于CGLib的动态代理．需要代理机制的原因是由于JDK本身只提供接口的代理，而不支持类的代理．

#### 4.Bean的生命周期

Spring中提供三种Bean生存的容器，BeanFactory,ApplicationContext,webApplicationContext,后两者基本一样．

![avatar](https://images2015.cnblogs.com/blog/937513/201605/937513-20160507202024015-234747937.png)

**BeanFactory中Bean生命周期的完整过程：**

- 当调用者通过getBean向容器请求bean时，如果容器注册了org.springframework.beans.factory.config.InstantiationAwareBeanPostProcessor接口，在实例Bean之前，调用该接口的postProcessBeforeInstantiation()方法
- 根据配置情况调用Bean构造函数或者工厂方法实例化Bean
- 如果容器注册了InstantiationAwareBeanPostProcessor接口，实例化之后调用该接口的postProcessAfterInstantiation()方法
- 如果Bean设置了属性信息，则设置每个属性前调用InstantiationAwareBeanPostProcess的postProcessPropertyValues()方法
- 设置属性值
- 如果Bean实现了BeanNameAware接口，将使用Bean的setBeanName()方法设置Bean的ID
- 如果Bean实现了BeanFactoryAware接口，则将调用setBeanFactory()方法传入自身
- 如果BeanPostProcessor和Bean关联，将调用BeanPostProcessor的postProcessBeforeInitialzation()方法对Bean进行加工
- 如果Bean实现了InitializingBean接口，调用afterPropertiesSet()方法
- 调用Init-method方法(如果指定了)
- 如果BeanPostProcessor和Bean关联，它的postProcessAfterInitialization()方法将被调用．此时，Bean可以被使用．
- 分支：
  - 如果在bean中指定了bean作用范围为scope="prototype"，将bean的调用者来管理Bean的生命周期，不由Spring管理
  - 如果在bean中指定了bean作用范围为scope="singleton"，将bean放入SpringIOC缓存池中，由Spring管理该Bean
- 如果Bean实现了DisposabelBean接口，afterPropertiesSet()方法被调用(Spring管理)
- 直接通过destroy-method销毁(Spring管理)

**总结：**

Bean的完整生命周期从 spring 容器开始实例化 bean 开始，到销毁。可以从三点来理解

1、 bean自身的方法：包括构造方法、 set 方法、 init-method 指定的方法、 destroy-method 指定的方法

2、 Bean级生命周期接口方法：如 BeanNameAware 、 BeanFactoryAware 等这些接口方法由 bean类实现。

3、 容器级生命周期接口方法：上图中带星的。有InstantiationAwareBeanPostProcessor 、 BeanPostProcessor 等。一般称为后处理 器。他们一般不由bean 本身实现，独立存在，注册到 spring 容器中。 Spring 通过接口反射预先知道，当 spring 容器创建任何 bean 时，这些后处理器都会发生作用。所以他们是全局的，用户可以通过编码对只感兴趣的 bean 进行处理。

**ApplicationContext:**

![avatar](https://images2015.cnblogs.com/blog/937513/201605/937513-20160507202059046-84853574.png)

 同BeanFactory相比，有两点不同：

- 实例化之前，如果配置文件中声明了工厂后处理器接口BeanFactoryPostProcessor实现类，则初始化bean之前对该接口配置信息进行加工
- 设置BeanFactory之后，调用ApplicationContextAware接口的setApplicationContext()方法



#### 5.Spring注解

- @Repository:将数据访问层标识为Spring Bean．该注解还能将所标注的类中抛出的数据访问异常封装成Spring的数据访问异常类型．
- @Controller:定义控制器类．在Spring中由控制器负责将用户发来的URL请求转发到对应的服务接口(service层)．通常需要配合注解＠RequestMapping．
- ＠RequestMapping：提供路由信息，负责URL到Controller的具体函数映射
- ＠Configuration:相当于传统的xml配置文件，如果有写三方库需要用到xml文件
- @ComponentScan:让Spring Boot扫描到Configuration类并将其加入到程序上下文，组件扫描，用于搜索bean
- ＠Autowired:自动导入
- ＠ResponseBody:该注解将方法的返回结果直接写入到Http response body中，用于构建RESTful的api
- ＠Service:用于修饰service层的组件
- @Bean:用该注解等价于xml中配置bean，放在方法上面，意思是产生一个bean并交由Spring管理
- @Value:注入配置的属性的值，用于将某个bean的变量注入
- ＠Component:泛指组件，当组件不好归类时，可以使用此注解进行标注
- ＠Resource(name="name",type="type")：与＠Autowired功能类似

#### 6.@Autowired和＠Resource区别

-  @Autowired与@Resource都可以用来装配bean. 都可以写在字段上,或写在setter方法上。
- @Autowired按byType自动注入，而@Resource默认按 byName自动注入
- ＠Autowired属于Spring,@Resource为JSR-250标准注释，属于J2EE的
- ＠Autowired默认按类型匹配，默认情况下有求依赖对象必须存在
- @Resource，默认安装名称进行装配，名称可以通过name属性进行指定，如果没有指定name属性，当注解写在字段上时，默认取字段名进行按照名称查找，如果注解写在setter方法上默认取属性名进行装配。当找不到与名称匹配的bean时才按照类型进行装配。但是需要注意的是，如果name属性一旦指定，就只会按照名称进行装配。

#### 7.@Controller和@RestController的区别

- 都使用来表示Spring某个类是否可以接收HTTP请求
- @Controller标识一个Spring类是Spring MVCcontroller处理器
- @RestController注解相当于@ResponseBody和@Controller的结合

#### 8.Spring依赖注入的方式

- 构造器注入
- 接口注入
- 属性注入(set方法)

#### 9.Spring IOC原理？自己实现IOC要怎么做，哪些步骤？

IOC:控制反转也叫依赖注入。利用了工厂模式
     将对象交给容器管理，你只需要在spring配置文件总配置相应的bean，以及设置相关的属性，让spring容器来生成类的实例对象以及管理对象。在spring容器启动的时候，spring会把你在配置文件中配置的bean都初始化好，然后在你需要调用的时候，就把它已经初始化好的那些bean分配给你需要调用这些bean的类（假设这个类名是A），分配的方法就是调用A的setter方法来注入，而不需要你在A里面new这些bean了。

#### 10.Spring中BeanFactory和ApplicationContext的区别？

BeanFactory:

​	Spring中最基本的Factory.无法支持Spring插件．是Spring IOC容器体系结构的基本接口．

ApplicationContext:

​	继承BeanFactory接口派生而来．提供了BeanFactory所有功能，并且提供更高级的功能

- MessageSource,提供国际化的消息访问
- 事件传播
- 资源访问，如URL和文件
- 载入多个上下文，使得每一个上下文都专注于一个特定的层次，比如应用的web层

#### 11.什么是IoC和DI？DI是如何实现的？

IoC叫控制反转，是Inversion of Control的缩写，DI（Dependency Injection）叫依赖注入，是对IoC更简单的诠释。控制反转是把传统上由程序代码直接操控的对象的调用权交给容器，通过容器来实现对象组件的装配和管理。所谓的”控制反转”就是对组件对象控制权的转移，从程序代码本身转移到了外部容器，由容器来创建对象并管理对象之间的依赖关系。

依赖注入可以通过setter方法注入（设值注入）、构造器注入和接口注入三种方式来实现，Spring支持setter注入和构造器注入，通常使用构造器注入来注入必须的依赖关系，对于可选的依赖关系，则setter注入是更好的选择，setter注入需要类提供无参构造器或者无参的静态工厂方法来创建对象。

#### 12.Spring中Bean的作用域有哪些？

Spring中Bean作用域有５种：

- singleton:该作用域使得IOC容器仅创建一个Bean实例，每次IOC容器返回都是同一个Bean实例
- prototype:IOC可以创建多个Bean实例，每次返回的都是一个新实例
- request:仅对HTTP请求起作用，使用该属性定义Bean时，每次HTTP请求都会创建一个新的Bean
- session:仅用于HTTP Session，同一个Session共享一个Bean实例．不同Session使用不同实例
- global-session:仅用于HTTP Session,所有的Session共享一个Bean实例

#### 13.Spring中自动装配的方式有哪些？

- no:不进行自动装配，手动设置Bean的依赖关系
- byName:根据Bean的名字进行自动装配
- byType:根据Bean的类型进行自动装配
- constructor:类似于byType,应用于构造器的参数，如果正好有一个Bean与构造器的参数类型相同则可以自动装配，否则报错
- autodetect:如果有默认的构造器，则通过constructor的方式进行自动配置，否则使用byType进行自动装配

使用条件：

使用JDK不需要三方库支持，只要JDK环境就可以进行代理：

- 实现InvocationHandler
- 使用Proxy.newProxyInstance产生代理对象
- 被代理的对象需要实现接口

CGLib必须依赖于CGLib的类库，但是它需要类来实现任何接口代理的是指定的类生成一个子类，覆盖其中的方法，是一种继承但是针对接口编程的环境下推荐使用JDK的代理；

#### 14.aop的应用场景？

权限控制，缓存，内容传递，错误处理，调试，记录跟踪，性能优化，资源池，同步，事务，持久化

#### 15.AOP的原理是什么？

动态代理

两种代理方式区别：

- JDK的动态代理针对实现了接口的类生成代理，不能针对类
- CGLIB针对类实现代理，主要针对指定的类生成一个子类，覆盖其中的方法实现增强，原类最好不要有final方法，否则无法继承

#### 16.Spring支持的事务管理类型

- 编程式事务管理：这意味你通过编程的方式管理事务，给你带来极大的灵活性，但是难维护。
- 声明式事务管理：这意味着你可以将业务代码和事务管理分离，你只需用注解和XML配置来管理事务。

#### 17.Spring框架优点

- 非侵入式设计：降低代码对框架的依赖
- 方便解耦，简化开发：对象的创建和依赖关系的维护工作都交由Spring容器管理，降低耦合
- 支持AOP:允许将一些通用任务进行集中式处理，提高程序复用性
- 支持声明式事务处理：通过配置就可以完成对事物的管理，无须手动编程
- 方便测试：支持Junit4测试框架
- 方便集成其他框架：Spring提供对其他框架的支持
- 降低Java EE APi使用难度

#### 18.持久层设计要考虑的问题有哪些

- 数据存储逻辑分离，提供抽象化的数据访问接口
- 数据访问底层实现的分离，可以在不修改代码的情况下更换底层实现
- 数据管理和调度的分离，在数据访问层实现统一的资源调度
- 数据抽象，提供面向对象的数据操作

#### 19.谈谈MVC，定义和执行流程

MVC(model-view-controller)模型－视图－控制器的缩写．它是一种软件设计典范，用一种将业务逻辑，数据，现显示界面分离的方法组织代码．简化分组开发，有助于管理复杂应用程序．MVC是一种框架模式而不是设计模式

- Model：程序中用于处理应用程序数据逻辑的部分
- View:程序中处理数据显示的部分
- Controller:处理用户交互部分

处理请求流程：

- 用户发送请求到前端控制器，控制器根据请求信息来决定选择哪一个页面控制器进行处理并把请求委托给对方；
- 页面控制器接收到请求后进行功能处理，首先需要收集和绑定一个请求参数到一个命令对象，然后将命令对象委托给业务对象进行处理，处理完毕后返回一个ModelAndView．
- 前端控制器收回控制权，然后根据返回的ModelAndView进行视图渲染，并把模型数据传入以便视图渲染．
- 前端将相应返回给用户．



#### 20.SpringBoot与Spring区别

​	首先，SpringBoot是对Spring框架的扩展．

​	区别：

- 消除了Spring所需要的xml配置
- 嵌入Tomcat,Jetty和undertow容器，并且不需要显示部署它们
- 可以自动配置Spring,SpringBoot将原有的XML配置改为JAVA配置，将Bean注入改为使用注解注入的方式@Autowired，并将xml,properties浓缩在一个application.yml配置文件中
- SpringBoot可以建立独立的Spring应用程序
- 整合常用依赖开发库(开发库，例如spring-webmvc、jackson-json、validation-api和tomcat等)，提供POM以简化Maven配置．

#### 21.Spring MVC执行过程

![avatar](https://images2015.cnblogs.com/blog/791227/201611/791227-20161125140338768-995727439.png)

具体步骤：

- 1.前端控制器收到请求(DispatcherServlet)
- 2.前端控制器请求HandlerMapping查找Handler
- 3.处理器映射器HandlerMapping向前端控制器返回Handler
- 4.前端控制器调用处理器适配器去执行Handler
- 5.处理器适配器HandlerAdapter根据适配的结果去执行Handler
- 6.Handler执行完成，返回ModelAndView给适配器
- 7.处理器适配器向前端控制器返回ModelAndView(一个对象，包括model和view)
- 8.前端控制器请求视图解析器进行视图解析
- 9.视图解析器向前端控制器返回View
- 10.前端控制器进行视图渲染
- 11.前端控制器向用户返回结果

#### 22.Spring启动过程

Spring启动是建筑在servlet容器上的，在web.xml中配置了servlet的上下文(context)和监听器(listener)．

Spring启动其实就是IOC容器启动过程．启动方式：

１.Spring的上下文监听器

- 调用Spring包中的ContextLoaderListener这个上下文监听器，项目启动时会触发该监听器中的contextInitialized()上下文初始化方法．该方法调用initWebApplicationContext(event.getServletContext())方法，进行ApplicationContext的初始化．
- initWebApplicationContext方法中主要做了三件事
  - 创建WebApplicationContext
  - 加载对应的Spring配置文件中的Bean
  - 将WebApplicationContext放入ServletContext中

2.SpringMVC启动过程

在contextLoaderListener监听器初始化完毕后，开始初始化web.xml配置中的Servlet，这里是DispatcherServlet.

- 起始位置为DispatcherServlet，继承关系：
  - DispatcherServlet  <-  FrameworkServlet  <-  HttpServletBean  <-  HttpServlet  <-  GenericServlet
- web容器在启动的时候会调用HttpServletBean的init()方法，该方法作用为：设置Servlet的初始化参数(contextAttribute,contextClass, namespace,  contextConfigLocation)．
- init()方法中调用的initServletBean()方法被FrameworkServlet中该方法覆盖．该方法作用为：初始化web上下文，提供给子类初始化扩展点

**总结**

1.首先，对于一个web应用，其部署在web容器中，web容器提供其一个全局的上下文环境，这个上下文就是ServletContext，其为后面的spring IoC容器提供宿主环境；

2.其 次，在web.xml中会提供有`contextLoaderListener`。在web容器启动时，会触发容器初始化事件，此时 `contextLoaderListener`会监听到这个事件，其`contextInitialized`方法会被调用，在这个方法中，spring会初始 化一个启动上下文，这个上下文被称为根上下文，即`WebApplicationContext`，这是一个接口类，确切的说，其实际的实现类是 `XmlWebApplicationContext`。这个就是spring的IoC容器，其对应的Bean定义的配置由web.xml中的 context-param标签指定。在这个IoC容器初始化完毕后，spring以`WebApplicationContext.ROOTWEBAPPLICATIONCONTEXTATTRIBUTE`为属性Key，将其存储到ServletContext中，便于获取；

3.再 次，`contextLoaderListener`监听器初始化完毕后，开始初始化web.xml中配置的Servlet，这里是DispatcherServlet，这个servlet实际上是一个标准的前端控制器，用以转发、匹配、处理每个servlet请 求。DispatcherServlet上下文在初始化的时候会建立自己的IoC上下文，用以持有spring mvc相关的bean。在建立DispatcherServlet自己的IoC上下文时，会利用`WebApplicationContext.ROOTWEBAPPLICATIONCONTEXTATTRIBUTE`先从ServletContext中获取之前的根上下文(即WebApplicationContext)作为自己上下文的parent上下文。有了这个 parent上下文之后，再初始化自己持有的上下文。这个DispatcherServlet初始化自己上下文的工作在其initStrategies方 法中可以看到，大概的工作就是初始化处理器映射、视图解析等。这个servlet自己持有的上下文默认实现类也是 `XmlWebApplicationContext`。初始化完毕后，spring以与servlet的名字相关(此处不是简单的以servlet名为 Key，而是通过一些转换，具体可自行查看源码)的属性为属性Key，也将其存到ServletContext中，以便后续使用。这样每个servlet 就持有自己的上下文，即拥有自己独立的bean空间，同时各个servlet共享相同的bean，即根上下文(第2步中初始化的上下文)定义的那些 bean．

#### 23.SpringBoot启动流程

概述：SpringBoot中启动流程主要分为三个部分：

- 进行SpringApplication初始化模块．配置一些基本的环境变量，资源，构造器，监听器等
- 实现具体应用的启动方案，包括监听模块，加载配置环境模块，核心的创建上下文模块
- 自动化配置模块

//TODO

































