### Springboot 自动装配原理

Spring Boot 通过`@EnableAutoConfiguration`开启自动装配，通过 SpringFactoriesLoader 最终加载`META-INF/spring.factories`中的自动配置类实现自动装配，自动配置类其实就是通过`@Conditional`按需加载的配置类，想要其生效必须引入`spring-boot-starter-xxx`包实现起步依赖

### @Target @Retention @Documented  @inherited注解

1. `@Target`:用于定义注解的使用位置
2. `@Retention`:用于指明修饰的注解的生存周期
   			1.   `source` : 源码级别保留，编译后即丢弃
      			1.   `CLASS`:编译级别保留，编译后的class文件中存在，在jvm运行时丢弃，这是默认值。
      			1.   `RUNTIME`:运行级别保留，编译后的class文件中存在，在jvm运行时保留，可以被反射调用。

3. `@Documented  `:指明修饰的注解，可以被例如javadoc此类的工具文档化，只负责标记，没有成员取值。
4. `@inherited`:用于标注一个父类的注解是否可以被子类继承，如果一个注解需要被其子类所继承，则在声明时直接使用@Inherited注解即可。如果没有写此注解，则无法被子类继承。

### Spring IOC 和 AOP

#### IOC   控制反转   依赖注入(DI)  同一个概念。

​			把**创建和查找依赖对象的控制权交给了容器，由容器进行注入组合对象**，所以**对象与对象之间是松散耦合**



#### AOP 面向切面编程

​			把与核心业务逻辑无关的代码全部**抽取出来**，放置到某个地方集中管理，然后在具体运行时，再由**容器动态织入这些共有代码**。

##### AOP的两种实现方式

1. jdk动态代理
2. cglib动态代理

### jdk动态代理和cglib的区别

1. jdk动态代理只能对实现了接口的类生成代理，而不能针对类。
2. CGlib是针对类实现代理。主要是对指定的类生成一个子类，覆盖其中的方法，并覆盖其中方法实现增强。将代理对象类的class文件加载进来，通过修改其字节码生成子类来处理。

####  为什么jdk动态代理只能基于接口？

生成的代理对象继承了`Proxy`类。 java不支持多继承，而jdk的动态代理在创建代理对象时，默认让代理对象继承了`Proxy`类，所以JDK只能通过接口去实现动态代理。

### Spring的**循环依赖问题**

1. 构造器注入无法解决循环依赖。可以使用==`@Lazy`==注解实现延迟加载，@Lazy实现原理是，当实例化对象时，如果发现参数或者属性有@Lazy注解修饰，那么就不直接创建所依赖的对象了，而是使用动态代理创建一个代理类。

​			比如，类A的创建：A a=new A(B)，需要依赖对象B，发现构造函数的形参上有@Lazy注解，那么就不直接创建B了，而是使用动态代理创建了一个代理类B1，此时A跟B就不是相互依赖了，变成了A依赖一个代理类B1，B依赖A。但因为在注入依赖时，类A并没有完全的初始化完，实际上注入的是一个代理对象，只有当他首次被使用的时候才会被完全的初始化。

1. setter注入且是在单例模式下不存在循环依赖问题。
2. setter注入且是在多例模式下无法解决循环依赖。

### Spring 三级缓存

| 名称                  | 作业                                                         |
| --------------------- | ------------------------------------------------------------ |
| singletonObjects      | 一级缓存，存放完整的 Bean。                                  |
| earlySingletonObjects | 二级缓存，存放提前暴露的Bean，Bean 是不完整的，未完成属性注入和执行 初始化（init） 方法。 |
| singletonFactories    | 三级缓存，存放的是 Bean 工厂，主要是生产 Bean，存放到二级缓存中。 |

Spring通过三级缓存解决了循环依赖，其中一级缓存为单例池（`singletonObjects`）,二级缓存为早期曝光对象`earlySingletonObjects`，三级缓存为早期曝光对象工厂（`singletonFactories`）。当A、B两个类发生循环引用时，在A完成实例化后，就使用实例化后的对象去创建一个对象工厂，并添加到三级缓存中，如果A被AOP代理，那么通过这个工厂获取到的就是A代理后的对象，如果A没有被AOP代理，那么这个工厂获取到的就是A实例化的对象。当A进行属性注入时，会去创建B，同时B又依赖了A，所以创建B的同时又会去调用getBean(a)来获取需要的依赖，此时的getBean(a)会从缓存中获取，第一步，先获取到三级缓存中的工厂；第二步，调用对象工工厂的getObject方法来获取到对应的对象，得到这个对象后将其注入到B中。紧接着B会走完它的生命周期流程，包括初始化、后置处理器等。当B创建完后，会将B再注入到A中，此时A再完成它的整个生命周期。至此，循环依赖结束！



”为什么要使用三级缓存呢？二级缓存能解决循环依赖吗？“

答：如果要使用二级缓存解决循环依赖，意味着所有Bean在实例化后就要完成AOP代理，这样违背了Spring设计的原则，Spring在设计之初就是通过`AnnotationAwareAspectJAutoProxyCreator`这个后置处理器来在Bean生命周期的最后一步来完成AOP代理，而不是在实例化后就立马进行AOP代理。



### Servlet三大组件

##### Servlet

​	Servlet是用来处理客户端请求的动态资源，也就是当我们在浏览器中键入一个地址回车跳转后，请求就会被发送到对应的Servlet上进行处理。

##### Filter

​	在执行Servlet之前执行，可以在Filter中进行日志输出，权限校验等。多个Filter会组成FilterChian，也就是在调用chain.doFilter(res,resp)方法时，会传给下一个过滤器（如果有），如果没有则，会访问servlet。servlet访问完之后，就会执行chan.doFilter之后的代码，也就是整个过程是在一个线程执行的。
**servlet主要负责处理请求，而filter主要负责拦截请求**

##### Listener

​	它可以监听Application、Session、Request对象，当这些对象发生变化就会调用对应的监听方法。

### sping中有哪些模块

1. Spring Core   	框架的最基础部分，提供 IoC 容器，对 bean 进行管理
2. Spring Context       基于 bean，提供上下文信息
3. Spring DAO     提供了JDBC的抽象层，它可消除冗长的JDBC编码和解析数据库厂商特有的错误代码，还提供了声明性事务管理方法  
4. Spring ORM    提供了常用的“对象/关系”映射APIs的集成层。 
5. Spring AOP    提供了符合AOP Alliance规范的面向方面的编程实现。  
6. Spring Web    提供了基础的 Web 开发的上下文信息
7. Spring Web MVC   提供了 Web 应用的 Model-View-Controller 全功能实现。

### Spring单例模式是线程安全的吗

​		若每个线程中对全局变量、静态变量只有读操作，而无写操作，那么不会有线程安全问题；

​		若有多个线程同时执行写操作，一般都需要考虑线程同步，否则就可能影响线程安全。

#### 		**无状态bean和有状态bean**

- 有实例变量的bean，可以保存数据，是非线程安全的。
- 没有实例变量的对象。不能保存数据，是线程安全的。

在Spring中无状态的Bean适合用单例模式，这样可以共享实例提高性能。有状态的Bean在多线程环境下不安全，一般用Prototype模式或者使用ThreadLocal解决线程安全问题。													

### spring mvc有哪些组件

1. 前端控制器（DispatcherServlet） 
2. 处理器映射器(HandlerMapping) 
3. 处理器适配器(HandlerAdapter) 
4. 视图解析器(ViewResolver) 
5. 视图渲染(View) 