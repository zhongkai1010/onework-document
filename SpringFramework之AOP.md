# SpringFramework之AOP
# 一、参考资料
[Spring AOP——Spring 中面向切面编程](https://www.cnblogs.com/joy99/p/10941543.html)

[Spring 之AOP AspectJ切入点语法详解](https://blog.csdn.net/jinnianshilongnian/article/details/84156354)

[Spring AOP原理详解及实例](https://blog.csdn.net/weixin_43611145/article/details/89084019)

# 二、AOP 概念
面向切面编程 (AOP) 通过提供另一种思考程序结构的方式来补充面向对象编程 (OOP)。 OOP 中模块化的关键单位是类，而 AOP 中模块化的单位是切面。 切面能够实现跨越多种类型和对象的关注点（例如事务管理）的模块化。 

SpringAOP使用了两种代理机制，一种是基于JDK的动态代理，另一种是基于CGLib的动态代理，之所以需要两种代理机制，很大程度上是因为JDK本身只提供基于接口的代理，不支持类的代理。

## （1）AOP 使用场景
* Authentication 权限
* Caching 缓存
* Context passing 内容传递
* Error handling 错误处理
* Lazy loading 懒加载
* Debugging 调试
* logging, tracing, profiling and monitoring 记录跟踪　优化　校准
* Performance optimization 性能优化
* Persistence 持久化
* Resource pooling 资源池
* Synchronization 同步
* Transactions 事务

## （2）AOP 特性术语
* 切面（Aspect）：一个关注点的模块化，这个关注点可能会横切多个对象。事务管理是J2EE应用中一个关于横切关注点的很好的例子。在Spring AOP中，切面可以使用基于模式或者基于@Aspect注解的方式来实现。
* 连接点（Joinpoint）: 在程序执行过程中某个特定的点，比如某方法调用的时候或者处理异常的时候。在Spring AOP中，一个连接点总是表示一个方法的执行。
* 通知/增强（Advice）: 在特定的连接点，AOP框架执行的动作。各种类型的通知包括“around”、“before”和“throws”通知。通知类型将在下面讨论。许多AOP框架包括Spring都是以拦截器做通知模型，维护一个“围绕”连接点的拦截器链。spring aop advice的类型：
   * 前置通知（before advice），在目标方法执行之前执行。
   * 返回后通知（after returning advice），在方法正常执行结束之后的通知，可以访问到方法的返回值。
   * 抛出异常后通知（after throwing advice），在目标方法出现异常时执行的代码，可以访问到异常对象，且可以指定出现特定异常执行此方法。
   * 后置通知：（after\[finally\] advice），在目标方法执行之后执行（无论是否发生异常）。
   * 环绕通知：（around advice），可以实现上述所有功能。
* 切入点（Pointcut）: 指定一个通知将被引发的一系列连接点的集合。AOP框架必须允许开发者指定切入点：例如，使用正则表达式。 Spring定义了Pointcut接口，用来组合MethodMatcher和ClassFilter，可以通过名字很清楚的理解， MethodMatcher是用来检查目标类的方法是否可以被应用此通知，而ClassFilter是用来检查Pointcut是否应该应用到目标类上
* 引入（Introduction）: 添加方法或字段到被通知的类。 Spring允许引入新的接口到任何被通知的对象。例如，你可以使用一个引入使任何对象实现 IsModified接口，来简化缓存。Spring中要使用Introduction, 可有通过DelegatingIntroductionInterceptor来实现通知，通过DefaultIntroductionAdvisor来配置Advice和代理类要实现的接口
* 目标对象（Target Object）: 包含连接点的对象。也被称作被通知或被代理对象。POJO
* AOP代理（AOP Proxy）: AOP框架创建的对象，包含通知。 在Spring中，AOP代理可以是JDK动态代理或者CGLIB代理。
* 织入（Weaving）: 组装方面来创建一个被通知对象。这可以在编译时完成（例如使用AspectJ编译器），也可以在运行时完成。Spring和其他纯Java AOP框架一样，在运行时完成织入。

# 三、AspectJ和Spring AOP的区别
**Spring AOP**

* 基于动态代理来实现，默认如果使用接口的，用JDK提供的动态代理实现，如果是方法则使用CGLIB实现
* Spring AOP需要依赖IOC容器来管理，并且只能作用于Spring容器，使用纯Java代码实现
* 在性能上，由于Spring AOP是基于动态代理来实现的，在容器启动时需要生成代理实例，在方法调用上也会增加栈的深度，使得Spring AOP的性能不如AspectJ的那么好

**AspectJ**

* AspectJ属于静态织入，通过修改代码来实现，有如下几个织入的时机：  
编译时织入：AspectJ编译器同时加载我们切面的源代码和我们的应用程序，并生成一个织入后的类文件作为输出。
编译后织入：这就是所熟悉的二进制织入。它被用来编织现有的类文件和JAR文件与我们的切面。
加载时织入：这和之前的二进制编织完全一样，所不同的是织入会被延后，直到类加载器将类加载到JVM。
* AspectJ可以做Spring AOP干不了的事情，它是AOP编程的完全解决方案，Spring AOP则致力于解决企业级开发中最普遍的AOP（方法织入）。而不是成为像AspectJ一样的AOP方案  
* 因为AspectJ在实际运行之前就完成了织入，所以说它生成的类是没有额外运行时开销的

**Spring AOP 和 AspectJ 之间的关键区别：**

|Spring AOP|AspectJ|
| ----- | ----- |
|在纯 Java 中实现|使用 Java 编程语言的扩展实现|
|不需要单独的编译过程|除非设置 LTW，否则需要 AspectJ 编译器 (ajc)|
|只能使用运行时织入|运行时织入不可用。支持编译时、编译后和加载时织入|
|功能不强-仅支持方法级编织|更强大 - 可以编织字段、方法、构造函数、静态初始值设定项、最终类/方法等......。|
|只能在由 Spring 容器管理的 bean 上实现|可以在所有域对象上实现|
|仅支持方法执行切入点|支持所有切入点|
|代理是由目标对象创建的, 并且切面应用在这些代理上|在执行应用程序之前 (在运行时) 前, 各方面直接在代码中进行织入|
|比 AspectJ 慢|更好的性能|
|易于学习和应用|相对于 Spring AOP 来说更复杂|
# 三、AOP 示例
要使用 Java `@Configuration` 启用 @AspectJ 支持，请添加 `@EnableAspectJAutoProxy` 注解，如以下示例所示：

```java
@Configuration
@EnableAspectJAutoProxy
public class AppConfig {

}
```
要使用基于 XML 的配置启用 @AspectJ 支持，请使用 `aop:aspectj-autoproxy` 元素，如以下示例所示：

```xml
<aop:aspectj-autoproxy/>
```
# 三、execution表达式
execution(\* com.sample.service.impl..\*.\*(..))
**解释如下：**

|**符号**|**含义**|
| ----- | ----- |
|execution（）|表达式的主体；|
|第一个”*“符号*|表示返回值的类型任意；|
|com.sample.service.impl|AOP所切的服务的包名，即，我们的业务部分|
|包名后面的”..“|表示当前包及子包|
|第二个”\*“|表示类名，\*即所有类。此处可以自定义，下文有举例|
|.\*(..)|表示任何方法名，括号表示参数，两个点表示任何参数类型|



## 