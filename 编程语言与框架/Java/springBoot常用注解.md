springBoot
======


### **一、什么是SpringBoot?**

SpringBoot 是一个快速开发框架，快速的将一些常用的第三方依赖整合（原理：通过 Maven 子父工程的方式），简化 XML 配置，全部采用注解形式，内置 Http 服务器（ Jetty 和 Tomcat ），最终以 java 应用程序进行执行。



### **二、SpringBoot** **核心原理**

1. 基于 SpringMVC 无配置文件（纯 Java ）完全注解化 + 内置 tomcat-embed-core 实现 SpringBoot 框架， Main 函数启动。

2. SpringBoot 核心快速整合第三方框架原理 :Maven 继承依赖关系。



### **三、SpringBoot** **重点**

3.1: 快速整合第三方依赖： maven 子父依赖关系。

springboot 通过引用 spring-boot-starter-web 依赖，整合 SpingMVC 框架。只需要引用一个 jar 包，就可以通过 Maven 继承的方式引用到Spring-aop,Spring-beans,Spring-core,Spring-web 等相关依赖。

```xml
<parent>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-parent</artifactId>
<version>2.0.0.RELEASE</version>
</parent>

<dependencies>
<!-- SpringBoot 整合 SpringMVC -->
<!-- 为什么我们映入 spring-boot-starter-web 能够帮我整合 Spring 环境 原理通过 Maven 子父工程 -->
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-web</artifactId>
</dependency>
</dependencies>
```



springBoot 框架流程：先创建 Tomcat 容器，然后加载 class 文件，加载过程中如果发现有 java 代码编写的 SpringMVC 初始化，就会创建SpringMVC 容器。所有程序执行完毕后，项目就可以访问了。



### **四、SpringBoot** **常用注解**

##### 1 、 @RestController 和 @RequestMapping 注解

@RestController 注解，它继承自 @Controller 注解。 4.0 之前的版本， Spring MVC 的组件都使用 @Controller 来标识当前类是一个控制器 servlet 。使用这个特性，我们可以开发 REST 服务的时候不需要使用 @Controller 而专门的 @RestController 。

当你实现一个RESTful web services 的时候， response 将一直通过 response body 发送。为了简化开发， Spring 4.0 提供了一个专门版本的 controller 。下面我们来看看 @RestController 实现的定义：

@Target(value=TYPE)

@Retention(value=RUNTIME)

@Documented

@Controller

@ResponseBody

public @interface RestController

注： @RestController 和 @RequestMapping 注解是 Spring MVC 注解（它们不是 Spring Boot 的特定部分）

##### 2 、 @EnableAutoConfiguration 注解

第二个类级别的注解是 @EnableAutoConfiguration 。这个注解告诉 Spring Boot 根据添加的 jar 依赖猜测你想如何配置 Spring 。由于spring-boot-starter-web 添加了 Tomcat 和 Spring MVC ，所以 auto-configuration 将假定你正在开发一个 web 应用并相应地对 Spring 进行设置。Starter POMs 和 Auto-Configuration ：设计 auto-configuration 的目的是更好的使用 "Starter POMs" ，但这两个概念没有直接的联系。你可以自由地挑选 starter POMs 以外的 jar 依赖，并且 Spring Boot 将仍旧尽最大努力去自动配置你的应用。

你可以通过将 @EnableAutoConfiguration 或 @SpringBootApplication 注解添加到一个 @Configuration 类上来选择自动配置。

注：你只需要添加一个 @EnableAutoConfiguration 注解。我们建议你将它添加到主 @Configuration 类上。

如果发现应用了你不想要的特定自动配置类，你可以使用 @EnableAutoConfiguration 注解的排除属性来禁用它们。

```java
import org.springframework.boot.autoconfigure.*;
import org.springframework.boot.autoconfigure.jdbc.*;
import org.springframework.context.annotation.*;

@Configuration

@EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})

public class MyConfiguration {

}
```



##### 3 、 @Configuration

Spring Boot 提倡基于 Java 的配置。尽管你可以使用一个 XML 源来调用 SpringApplication.run() ，我们通常建议你使用 @Configuration 类作为主要源。一般定义外汇返佣[http://www.fx61.com/](https://link.zhihu.com/?target=http%3A//www.fx61.com/) 方法的类也是主要 @Configuration 的一个很好候选。你不需要将所有的 @Configuration 放进一个单独的类。 @Import 注解可以用来导入其他配置类。另外，你也可以使用 @ComponentScan 注解自动收集所有的 Spring 组件，包括@Configuration 类。

如果你绝对需要使用基于XML 的配置，我们建议你仍旧从一个 @Configuration 类开始。你可以使用附加的 @ImportResource 注解加载 XML配置文件。

@Configuration 注解该类，等价 与 XML 中配置 beans ；用 @Bean 标注方法等价于 XML 中配置 bean

@ComponentScan(basePackages = "com.hyxt",includeFilters = {@ComponentScan.Filter(Aspect.class)})



##### 4 、 @SpringBootApplication

很多Spring Boot 开发者总是使用 @Configuration ， @EnableAutoConfiguration 和 @ComponentScan 注解他们的 main 类。由于这些注解被如此频繁地一块使用（特别是你遵循以上最佳实践时）， Spring Boot 提供一个方便的 @SpringBootApplication 选择。

该 @SpringBootApplication 注解等价于以默认属性使用 @Configuration ， @EnableAutoConfiguration 和 @ComponentScan 。

```java
package com.example.myproject;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication // same as @Configuration @EnableAutoConfiguration @ComponentScan
@ComponentScan
public class Application {

    public static void main(String[] args) {
      		SpringApplication.run(Application.class, args);
    }
}
```

Spring Boot 将尝试校验外部的配置，默认使用 JSR-303 （如果在 classpath 路径中）。你可以轻松的为你的 @ConfigurationProperties 类添加 JSR-303 javax.validation 约束注解：

```java
@Component
@ConfigurationProperties(prefix="connection")
public class ConnectionSettings {
    @NotNull
    private InetAddress remoteAddress;

    // ... getters and setters

}
```

##### 5 、 @ResponseBody

表示该方法的返回结果直接写入HTTP response body 中

一般在异步获取数据时使用，在使用@RequestMapping 后，返回值通常解析为跳转路径，加上

@responsebody 后返回结果不会被解析为跳转路径，而是直接写入 HTTP response body 中。比如

异步获取json 数据，加上 @responsebody 后，会直接返回 json 数据。

##### 6 、 @Component

泛指组件，当组件不好归类的时候，我们可以使用这个注解进行标注。一般公共的方法我会用上这个注解

##### 7 、 @AutoWired

byType 方式。把配置好的 Bean 拿来用，完成属性、方法的组装，它可以对类成员变量、方法及构

造函数进行标注，完成自动装配的工作。

当加上（required=false ）时，就算找不到 bean 也不报错。

##### 8 、 @RequestParam

用在方法的参数前面：

@RequestParam String a =request.getParameter("abc")

##### 9 、 @PathVariable

路径变量。

```java
@RequestMapping("user/get/mac/{macAddress}")
public String getByMacAddress(@PathVariable String macAddress){

//can do something;

}
```

参数与大括号里的名字一样要相同。

以上注解的示范

```java
/**
* 用户进行评论及对评论进行管理的 Controller 类；
*/
@Controller
@RequestMapping("/msgCenter")
public class MyCommentController extends BaseController {

    @Autowired
    CommentService commentService;

    @Autowired
    OperatorService operatorService;

    /**
    * 添加活动评论；
    *
    * @param applyId 活动 ID ；
    * @param content 评论内容；
    * @return
    */
    @ResponseBody
    @RequestMapping("/addComment")
    public Map<String, Object> addComment(@RequestParam("applyId") Integer applyId,	       @RequestParam("content") String content) {
        ....
        return result;
    }

}
```

##### 10 、 @ControllerAdvice

全局处理异常的：

@ControllerAdvice

包含@Component 。可以被扫描到。

统一处理异常。

@ExceptionHandler （ Exception.class ）：

用在方法上面表示遇到这个异常就执行以下方法。

```java
/**
* 全局异常处理
*/
@ControllerAdvice
class GlobalDefaultExceptionHandler {

    public static final String DEFAULT_ERROR_VIEW = "error";

    @ExceptionHandler({TypeMismatchException.class,NumberFormatException.class})
    public ModelAndView formatErrorHandler(HttpServletRequest req, Exception e) throws Exception {
        ModelAndView mav = new ModelAndView();
        mav.addObject("error"," 参数类型错误 ");
        mav.addObject("exception", e);
        mav.addObject("url", RequestUtils.getCompleteRequestUrl(req));
        mav.addObject("timestamp", new Date());
        mav.setViewName(DEFAULT_ERROR_VIEW);
        return mav;
    }
}
```

##### 11 、 @ComponentScan

做过web 开发的同学一定都有用过 @Controller ， @Service ， @Repository 注解，查看其源码你会发现，他们中有一个共同的注解@Component ，没错 @ComponentScan 注解默认就会装配标识了 @Controller ， @Service ， @Repository ， @Component 注解的类到 spring容器中。

```java
@ComponentScan(value = "com.abacus.check.api")
public class CheckApiApplication {

    public static void main(String[] args) {
        SpringApplication.run(CheckApiApplication.class, args);
    }
}
```

@SpringBootApplication 注解也包含了 @ComponentScan 注解，所以在使用中我们也可以通过 @SpringBootApplication 注解的scanBasePackages 属性进行配置。

```java
@SpringBootApplication(scanBasePackages = {"com.abacus.check.api", "com.abacus.check.service"})
public class CheckApiApplication {

    public static void main(String[] args) {
       SpringApplication.run(CheckApiApplication.class, args);
    }
}
```

##### 12 、 @Conditional

@Conditional 是 Spring4 新提供的注解，通过 @Conditional 注解可以根据代码中设置的条件装载不同的 bean ，在设置条件注解之前，先要把装载的 bean 类去实现 Condition 接口，然后对该实现接口的类设置是否装载的条件。 Spring Boot 注解中的 @ConditionalOnProperty 、@ConditionalOnBean 等以 @Conditional* 开头的注解，都是通过集成了 @Conditional 来实现相应功能的。

**@Conditional 的定义：**

```java
// 此注解可以标注在类和方法上 @Target({ElementType.TYPE, ElementType.METHOD}) 
@Retention(RetentionPolicy.RUNTIME) @Documented public @interface Conditional { 
  Class<?extends Condition>[] value(); 
}
```



从代码中可以看到，需要传入一个Class 数组，并且需要继承 Condition 接口：

```java
public interface Condition { boolean matches(ConditionContext var1, AnnotatedTypeMetadata var2); }
```



Condition 是个接口，需要实现 matches 方法，返回 true 则注入 bean ， false 则不注入。

示例：

首先，创建Person 类：

```java
public class Person {

    private String name;
    private Integer age;
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public Integer getAge() { return age; }
    public void setAge(Integer age) { this.age = age; }
    public Person(String name, Integer age) { this.name = name; this.age = age; }

    @Override
    public String toString() { 
      return "Person{" + "name='" + name + '\'' + ", age=" + age + '}'; 
    } 
}

//创建BeanConfig 类，用于配置两个 Person 实例并注入，一个是比尔盖茨，一个是林纳斯。
@Configuration
public class BeanConfig {
    @Bean(name = "bill")
    public Person person1(){ return new Person("Bill Gates",62); }

    @Bean("linus")
    public Person person2(){ return new Person("Linus",48); }
}
```



接着写一个测试类进行验证这两个Bean 是否注入成功。

```java
public class ConditionalTest {
		AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(BeanConfig.class);

    @Test
    public void test1(){
        Map<String, Person> map = applicationContext.getBeansOfType(Person.class);
        System.out.println(map);
    }
}
```



运行，输出结果是这样的，两个 Person 实例被注入进容器。

运行，输出结果是这样的，两个Person 实例被注入进容器。

这是一个简单的例子，现在问题来了，如果我想根据当前操作系统来注入Person 实例， windows 下注入 bill ， linux 下注入 linus ，怎么实现呢？

这就需要我们用到 @Conditional 注解了，前言中提到，需要实现 Condition 接口，并重写方法来自定义 match 规则。

首先，创建一个WindowsCondition 类：

```java
public class WindowsCondition implements Condition {

    /**
    \* @param conditionContext: 判断条件能使用的上下文环境
    \* @param annotatedTypeMetadata: 注解所在位置的注释信息
    \* */
    @Override public boolean matches(ConditionContext conditionContext, AnnotatedTypeMetadata annotatedTypeMetadata) {

        // 获取 ioc 使用的 beanFactory
        ConfigurableListableBeanFactory beanFactory = conditionContext.getBeanFactory();

        // 获取类加载器
        ClassLoader classLoader = conditionContext.getClassLoader();

        // 获取当前环境信息
        Environment environment = conditionContext.getEnvironment();

        // 获取 bean 定义的注册类
        BeanDefinitionRegistry registry = conditionContext.getRegistry();

        // 获得当前系统名
        String property = environment.getProperty("os.name");

        // 包含 Windows 则说明是 windows 系统，返回 true
        if (property.contains("Windows")){
            return true;
        }

        return false;

    }

}
```



matches 方法的两个参数的意思在注释中讲述了，值得一提的是， conditionContext 提供了多种方法，方便获取各种信息，也是 SpringBoot中 @ConditonalOnXX 注解多样扩展的基础。

接着，创建LinuxCondition 类：

```java
public class LinuxCondition implements Condition {

    @Override
    public boolean matches(ConditionContext conditionContext, AnnotatedTypeMetadata annotatedTypeMetadata) {
        Environment environment = conditionContext.getEnvironment();
        String property = environment.getProperty("os.name");
        if (property.contains("Linux")){
            return true;
        }

        return false;
    }
}
```



接着就是使用这两个类了，因为此注解可以标注在方法上和类上，所以分开测试：

标注在方法上：

修改BeanConfig ：

```java
@Configuration

public class BeanConfig {

    // 只有一个类时，大括号可以省略
    // 如果 WindowsCondition 的实现方法返回 true ，则注入这个 bean @Conditional({WindowsCondition.class})

    @Bean(name = "bill")
    public Person person1(){
    		return new Person("Bill Gates",62);
    }

    // 如果 LinuxCondition 的实现方法返回 true ，则注入这个 bean
    @Conditional({LinuxCondition.class})
    @Bean("linus")
    public Person person2(){
    		return new Person("Linus",48);
    }

}
```



修改测试方法，使其可以打印当前系统名：

运行结果如下：

> 我是运行在windows 上的所以只注入了 bill ，嗯，没毛病。

接着实验linux 下的情况，不能运行在 linux 下，但可以修改运行时参数：

修改后启动测试方法：

一个方法只能注入一个 bean 实例，所以 @Conditional 标注在方法上只能控制一个 bean 实例是否注入。

标注在类上：

一个类中可以注入很多实例，@Conditional 标注在类上就决定了一批 bean 是否注入。

我们试一下，将BeanConfig 改写，这时，如果 WindowsCondition 返回 true ，则两个 Person 实例将被注入 ( 注意：上一个测试将 os.name改为 linux ，这是我将把这个参数去掉 ) ：

```java
@Conditional({WindowsCondition.class})
@Configuration
public class BeanConfig {

    @Bean(name = "bill")
    public Person person1(){
        return new Person("Bill Gates",62);
    }

    @Bean("linus")
    public Person person2(){
        return new Person("Linus",48);
    }
}
```



结果两个实例都被注入：

如果将类上的WindowsCondition.class 改为 LinuxCondition.class ，结果应该可以猜到：

结果就是空的，类中所有bean 都没有注入。

多个条件类：

前言中说，@Conditional 注解传入的是一个 Class 数组，存在多种条件类的情况。

这种情况貌似判断难度加深了，测试一波，新增新的条件类，实现的matches 返回 false （这种写死返回 false 的方法纯属测试用，没有实际意义 O( ∩ _ ∩ )O ）

```java
public class ObstinateCondition implements Condition {

    @Override
    public boolean matches(ConditionContext conditionContext, AnnotatedTypeMetadata annotatedTypeMetadata) {
        return false;
    }
}
```

BeanConfig 修改一下：

```java
@Conditional({WindowsCondition.class,ObstinateCondition.class})
@Configuration
public class BeanConfig {

    @Bean(name = "bill")
    public Person person1(){
        return new Person("Bill Gates",62);
    }

    @Bean("linus")
    public Person person2(){
        return new Person("Linus",48);
    }
}
```

结果：

现在如果将ObstinateCondition 的 matches 方法返回值改成 true ，两个 bean 就被注入进容器：

结论得：

第一个条件类实现的方法返回true ，第二个返回 false ，则结果 false ，不注入进容器。

第一个条件类实现的方法返回true ，第二个返回 true ，则结果 true ，注入进容器中。



