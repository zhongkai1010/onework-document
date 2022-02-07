# Spring Boot 简要说明与使用总结
# 一、参考资料
Spring Boot 中文网：[https://springref.com/](https://springref.com/)

GitHub学习网址： [https://github.com/Snailclimb/springboot-guide/blob/master/docs/start/springboot-hello-world.md](https://github.com/Snailclimb/springboot-guide/blob/master/docs/start/springboot-hello-world.md)[https://www.logicbig.com/](https://www.logicbig.com/)



# 二、 Spring Boot 目录结构
工程启动类(ApplicationServer.java)置于com.springboot.build包下

实体类(domain)置于com.springboot.domain

数据访问层(Dao)置于com.springboot.repository

数据服务层(Service)置于com,springboot.service,数据服务的实现接口(serviceImpl)至于com.springboot.service.impl

前端控制器(Controller)置于com.springboot.controller

工具类(utils)置于com.springboot.utils

常量接口类(constant)置于com.springboot.constant

配置信息类(config)置于com.springboot.config

数据传输类(vo)置于com.springboot.vo

start启动器

## Spring Boot 核心功能
#### 1）独立运行的 Spring 项目
Spring Boot 可以以 jar 包的形式独立运行，运行一个 Spring Boot 项目只需通过 java–jar xx.jar 来运行。  

#### 2）内嵌 Servlet 容器
Spring Boot 可选择内嵌 Tomcat、Jetty 或者 Undertow，这样我们无须以 war 包形式部署项目。  

#### 3）提供 starter 简化 [Maven](http://c.biancheng.net/maven/) 配置
Spring 提供了一系列的 starter pom 来简化 Maven 的依赖加载，例如，当你使用了spring-boot-starter-web 时，会自动加入如图 1 所示的依赖包。  

#### 4）自动配置 Spring
Spring Boot 会根据在类路径中的 jar 包、类，为 jar 包里的类自动配置 Bean，这样会极大地减少我们要使用的配置。当然，Spring Boot 只是考虑了大多数的开发场景，并不是所有的场景，若在实际开发中我们需要自动配置 Bean，而 Spring Boot 没有提供支持，则可以自定义自动配置。  

#### 5）准生产的应用监控
Spring Boot 提供基于 http、ssh、telnet 对运行时的项目进行监控。  

#### 6）无代码生成和 xml 配置
Spring Boot 的神奇的不是借助于代码生成来实现的，而是通过条件注解来实现的，这是 Spring 4.x 提供的新特性。Spring 4.x 提倡使用 Java 配置和注解配置组合，而 Spring Boot 不需要任何 xml 配置即可实现 Spring 的所有配置。

# 三、Spring Boot 注解
## 1）常见注解
### **@SpringBootApplication**
@SpringBootApplication 注解一般放在项目的一个启动类上，用来把启动类注入到容器中，用来定义容器扫描的范围和加载classpath环境中一些bean，从@SpringBootApplication的源码中可以看出：

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = {
        @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
        @Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
```
### **@EnableAutoConfiguration**
Spring Boot自动配置（auto-configuration）：尝试根据你添加的jar依赖自动配置你的Spring应用。例如，如果你的classpath下存在HSQLDB，并且你没有手动配置任何数据库连接beans，那么我们将自动配置一个内存型（in-memory）数据库”。你可以将@EnableAutoConfiguration或者@SpringBootApplication注解添加到一个@Configuration类上来选择自动配置。如果发现应用了你不想要的特定自动配置类，你可以使用@EnableAutoConfiguration注解的排除属性来禁用它们。

### **@ComponentScan**
主要就是定义**扫描的路径**从中找出标识了**需要装配的类**自动装配到spring的bean容器中。如果扫描到有@Component、@Controller、@Service等这些注解的类，并注册为Bean，可以自动收集所有的Spring组件，包括@Configuration类。我们经常使用@ComponentScan注解搜索beans，并结合@Autowired注解导入。

@SpringBootApplication注解默认扫描只是当前项目的**本包**和**它的子包**,那就想办法让它扫描第三方jar的包就好了。

```java
/**
 * @Description: Springboot 启动类
 */
@ComponentScan(basePackages ={"com.third.bean"})
@SpringBootApplication()
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class);
    }
}
```
### **@Configuration**
相当于传统的xml配置文件，如果有些第三方库需要用到xml文件，建议仍然通过@Configuration类作为项目的配置主类——可以使用@ImportResource注解加载xml配置文件。

## 2）IOC 容器
### **@Component**
`@Component`注解是一个元注解，即可以标注在其它的注解上。在spring中，任何被`@Component`注解标识的组件均为组件扫描的候选对象，并且被`@Component`元注解标注的注解，在任何组件标注它时，也被视作组件扫描的候选对象。简单来说，就是在spring中，一个普通的javaBean被`@Component`注解标记后，在使用基于注解配置和类路径扫描时，会被作为候选组件，添加到spring容器中

```java
package com.spring.study.ioc.register;

/**
 * spring扫描的候选组件
 *
 * @author TangFD
 * @since 2019/6/25.
 */
@Data
@Component
public class TestComponent {
    private String id = "@Component";
}
```
### **@Bean**
Spring的@Bean注解用于告诉方法，产生一个Bean对象，然后这个Bean对象交给Spring管理。产生这个Bean对象的方法Spring只会调用一次，随后这个Spring将会将这个Bean对象放在自己的IOC容器中。

SpringIOC 容器管理一个或者多个bean，这些bean都需要在@Configuration注解下进行创建，在一个方法上使用@Bean注解就表明这个方法需要交给Spring进行管理。

```java
@Configuration
public class AppConfig {

      // 使用@Bean 注解表明myBean需要交给Spring进行管理
      // 未指定bean 的名称，默认采用的是 "方法名" + "首字母小写"的配置方式
    @Bean
    public MyBean myBean(){
        return new MyBean();
    }
}

public class MyBean {

    public MyBean(){
        System.out.println("MyBean Initializing");
    }
}
```
在对应的test文件夹下创建一个测试类`SpringBeanApplicationTests`，测试上述代码的正确性

```java
public class SpringBeanApplicationTests {

    public static void main(String[] args) {
        ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
        context.getBean("myBean");
    }
}
```
输出 : MyBean Initializing

### **@Autowired**
自动导入依赖的bean

### **@Import**
@Import引入普通的类可以帮助我们把普通的类定义为Bean。@Import可以添加在@SpringBootApplication(启动类)、@Configuration(配置类)、@Component(组件类)对应的类上。

### **@ImportResource**
用来加载xml配置文件。

### **@RepositoryRestResourcepublic**
配合spring-boot-starter-data-rest使用。

### **@Service**
一般用于修饰service层的组件

### **@Repository**
使用@Repository注解可以确保DAO或者repositories提供异常转译，这个注解修饰的DAO或者repositories类会被ComponetScan发现并配置，同时也不需要为它们提供XML配置项。

### **@Value**
注入Spring boot application.properties配置的属性的值。示例代码：

```java
@Value(value = “#{message}”)
private String message;
```
### **@Inject**
等价于默认的@Autowired，只是没有required属性；

### **@AutoWired**
自动导入依赖的bean。byType方式。把配置好的Bean拿来用，完成属性、方法的组装，它可以对类成员变量、方法及构造函数进行标注，完成自动装配的工作。当加上（required=false）时，就算找不到bean也不报错。

### **@Qualifier**
当有多个同一类型的Bean时，可以用@Qualifier(“name”)来指定。与@Autowired配合使用。@Qualifier限定描述符除了能根据名字进行注入，但能进行更细粒度的控制如何选择候选者，具体使用方式如下：

```Plain Text
@Autowired 
@Qualifier(value = “demoInfoService”)
private DemoInfoService demoInfoService;
```
### **@Resource(name=”name”,type=”type”)**
没有括号内内容的话，默认byName。与@Autowired干类似的事。

四、自动装配

@ConditionalOnMissingBean

@ConditionalOnBean

@ConditionalOnClass

# 四、解决问题记录
# 五、Spring中的各种Utils
# 六、Spring容器之ApplicationContext
