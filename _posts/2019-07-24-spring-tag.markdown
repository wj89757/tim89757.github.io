---
layout:     post
title:      "Spring体系结构"
subtitle:   "Spring体系结构"
date:       2019-07-24 11:22:00
author:     "Tim"
header-img: "img/post-bg-js-version.jpg"
tags:
    - MySQL 性能优化
---

## 一、Spring体系结构：

Spring 有可能成为所有企业应用程序的一站式服务点，然而，Spring 是模块化的，允许你挑选和选择适用于你的模块，不必要把剩余部分也引入。下面的部分对在 Spring 框架中所有可用的模块给出了详细的介绍。

Spring 框架提供约 20 个模块，可以根据应用程序的要求来使用。

![Spring 体系结构](https://7n.w3cschool.cn/attachments/image/wk/wkspring/arch1.png)

## 核心容器

核心容器由**spring-core，spring-beans，spring-context，spring-context-support和spring-expression**（SpEL，Spring表达式语言，Spring Expression Language）等模块组成，它们的细节如下：

- **spring-core**模块提供了框架的基本组成部分，包括 IoC 和依赖注入功能。
- **spring-beans** 模块提供 BeanFactory，工厂模式的微妙实现，它移除了编码式单例的需要，并且可以把配置和依赖从实际编码逻辑中解耦。
- **context**模块建立在由**core**和 **beans** 模块的基础上建立起来的，它以一种类似于JNDI注册的方式访问对象。Context模块继承自Bean模块，并且添加了国际化（比如，使用资源束）、事件传播、资源加载和透明地创建上下文（比如，通过Servelet容器）等功能。Context模块也支持Java EE的功能，比如EJB、JMX和远程调用等。**ApplicationContext**接口是Context模块的焦点。**spring-context-support**提供了对第三方库集成到Spring上下文的支持，比如缓存（EhCache, Guava, JCache）、邮件（JavaMail）、调度（CommonJ, Quartz）、模板引擎（FreeMarker, JasperReports, Velocity）等。
- **spring-expression**模块提供了强大的表达式语言，用于在运行时查询和操作对象图。它是JSP2.1规范中定义的统一表达式语言的扩展，支持set和get属性值、属性赋值、方法调用、访问数组集合及索引的内容、逻辑算术运算、命名变量、通过名字从Spring IoC容器检索对象，还支持列表的投影、选择以及聚合等。

它们的完整依赖关系如下图所示：

![Spring 体系结构](https://7n.w3cschool.cn/attachments/image/20181023/1540290875453691.png)

## 数据访问/集成

数据访问/集成层包括 JDBC，ORM，OXM，JMS 和事务处理模块，它们的细节如下：

（注：JDBC=Java Data Base Connectivity，ORM=Object Relational Mapping，OXM=Object XML Mapping，JMS=Java Message Service）

- **JDBC** 模块提供了JDBC抽象层，它消除了冗长的JDBC编码和对数据库供应商特定错误代码的解析。
- **ORM** 模块提供了对流行的对象关系映射API的集成，包括JPA、JDO和Hibernate等。通过此模块可以让这些ORM框架和spring的其它功能整合，比如前面提及的事务管理。
- **OXM** 模块提供了对OXM实现的支持，比如JAXB、Castor、XML Beans、JiBX、XStream等。
- **JMS** 模块包含生产（produce）和消费（consume）消息的功能。从Spring 4.1开始，集成了spring-messaging模块。。
- **事务**模块为实现特殊接口类及所有的 POJO 支持编程式和声明式事务管理。（注：编程式事务需要自己写beginTransaction()、commit()、rollback()等事务管理方法，声明式事务是通过注解或配置由spring自动处理，编程式事务粒度更细）

## Web

Web 层由 Web，Web-MVC，Web-Socket 和 Web-Portlet 组成，它们的细节如下：

- **Web** 模块提供面向web的基本功能和面向web的应用上下文，比如多部分（multipart）文件上传功能、使用Servlet监听器初始化IoC容器等。它还包括HTTP客户端以及Spring远程调用中与web相关的部分。。
- **Web-MVC** 模块为web应用提供了模型视图控制（MVC）和REST Web服务的实现。Spring的MVC框架可以使领域模型代码和web表单完全地分离，且可以与Spring框架的其它所有功能进行集成。
- **Web-Socket** 模块为 WebSocket-based 提供了支持，而且在 web 应用程序中提供了客户端和服务器端之间通信的两种方式。
- **Web-Portlet** 模块提供了用于Portlet环境的MVC实现，并反映了spring-webmvc模块的功能。

## 其他

还有其他一些重要的模块，像 [AOP](https://www.w3cschool.cn/wkspring/izae1h9w.html)，Aspects，Instrumentation，Web 和测试模块，它们的细节如下：

- **AOP** 模块提供了面向方面的编程实现，允许你定义方法拦截器和切入点对代码进行干净地解耦，从而使实现功能的代码彻底的解耦出来。使用源码级的元数据，可以用类似于.Net属性的方式合并行为信息到代码中。
- **Aspects** 模块提供了与 **AspectJ** 的集成，这是一个功能强大且成熟的面向切面编程（AOP）框架。
- **Instrumentation** 模块在一定的应用服务器中提供了类 instrumentation 的支持和类加载器的实现。
- **Messaging** 模块为 STOMP 提供了支持作为在应用程序中 WebSocket 子协议的使用。它也支持一个注解编程模型，它是为了选路和处理来自 WebSocket 客户端的 STOMP 信息。
- **测试**模块支持对具有 JUnit 或 TestNG 框架的 Spring 组件的测试。





## 二、Spring IOC容器:

![微信图片_20190626204151](E:\熙邻\技术分享\2019.08-spring\img\微信图片_20190626204151.png)



### IoC 容器

Spring有两个核心接口：BeanFactory和ApplicationContext，其中ApplicationContext是BeanFactory的子接口。他们都可代表Spring容器，Spring容器是生成Bean实例的工厂，并且管理容器中的Bean。

         Bean是Spring管理的基本单位，在基于Spring的Java EE应用中，所有的组件都被当成Bean处理，包括数据源、Hibernate的SessionFactory、事务管理器等。在Spring中，Bean的是一个非常广义的概念，任何的Java对象、Java组件都被当成Bean处理。

         而且应用中的所有组件，都处于Spring的管理下，都被Spring以Bean的方式管理，**Spring负责创建Bean实例，并管理他们的生命周期**。Bean在Spring容器中运行，无须感受Spring容器的存在，一样可以接受Spring的依赖注入，包括Bean属性的注入，协作者的注入、依赖关系的注入等。

         Spring容器负责创建Bean实例，所以需要知道每个Bean的实现类，Java程序面向接口编程，无须关心Bean实例的实现类；但是Spring容器必须能够精确知道每个Bean实例的实现类，因此Spring配置文件必须精确配置Bean实例的实现类。

###  BeanFactory 容器

这是一个最简单的容器，它主要的功能是为依赖注入 （DI） 提供支持，这个容器接口在 org.springframework.beans.factory.BeanFactor 中被定义。BeanFactory 和相关的接口，比如BeanFactoryAware、DisposableBean、InitializingBean，仍旧保留在 Spring 中，主要目的是向后兼容已经存在的和那些 Spring 整合在一起的第三方框架。

在 Spring 中，有大量对 BeanFactory 接口的实现。其中，最常被使用的是 **XmlBeanFactory** 类。这个容器从一个 XML 文件中读取配置元数据，由这些元数据来生成一个被配置化的系统或者应用。

在资源宝贵的移动设备或者基于 applet 的应用当中， BeanFactory 会被优先选择。否则，一般使用的是 ApplicationContext，除非你有更好的理由选择 BeanFactory。

### ApplicationContext 容器

Application Context 是 BeanFactory 的子接口，也被成为 Spring 上下文。 

Application Context 是 spring 中较高级的容器。和 BeanFactory 类似，它可以加载配置文件中定义的 bean，将所有的 bean 集中在一起，当有请求的时候分配 bean。 另外，它增加了企业所需要的功能，比如，从属性文件中解析文本信息和将事件传递给所指定的监听器。这个容器在 org.springframework.context.ApplicationContext interface 接口中定义。

ApplicationContext 包含 BeanFactory 所有的功能，一般情况下，相对于 BeanFactory，ApplicationContext 会更加优秀。当然，BeanFactory 仍可以在轻量级应用中使用，比如移动设备或者基于 applet 的应用程序。

最常被使用的 ApplicationContext 接口实现：

- **FileSystemXmlApplicationContext**：该容器从 XML 文件中加载已被定义的 bean。在这里，你需要提供给构造器 XML 文件的完整路径。
- **ClassPathXmlApplicationContext**：该容器从 XML 文件中加载已被定义的 bean。在这里，你不需要提供 XML 文件的完整路径，只需正确配置 CLASSPATH 环境变量即可，因为，容器会从 CLASSPATH 中搜索 bean 配置文件。
- **WebXmlApplicationContext**：该容器会在一个 web 应用程序的范围内加载在 XML 文件中已被定义的 bean。



## 三：Spring Bean定义：BeanDefinition

被称作 bean 的对象是构成应用程序的支柱也是由 Spring IoC 容器管理的。bean 是一个被实例化，组装，并通过 Spring IoC 容器所管理的对象。这些 bean 是由用容器提供的配置元数据创建的，例如，已经在先前章节看到的，在 XML 的表单中的 定义。

bean 定义包含称为**配置元数据**的信息，下述容器也需要知道配置元数据：

- 如何创建一个 bean
- bean 的生命周期的详细信息
- bean 的依赖关系

上述所有的配置元数据转换成一组构成每个 bean 定义的下列属性。

| 属性                       | 描述                                       |
| ------------------------ | ---------------------------------------- |
| class                    | 这个属性是强制性的，并且指定用来创建 bean 的 bean 类。        |
| name                     | 这个属性指定唯一的 bean 标识符。在基于 XML 的配置元数据中，你可以使用 ID 和/或 name 属性来指定 bean 标识符。 |
| scope                    | 这个属性指定由特定的 bean 定义创建的对象的作用域，它将会在 bean 作用域的章节中进行讨论。 |
| constructor-arg          | 它是用来注入依赖关系的                              |
| properties               | 它是用来注入依赖关系的                              |
| autowiring mode          | 它是用来注入依赖关系的                              |
| lazy-initialization mode | 延迟初始化的 bean 告诉 IoC 容器在它第一次被请求时，而不是在启动时去创建一个 bean 实例。 |
| initialization 方法        | 在 bean 的所有必需的属性被容器设置之后，调用回调方法。它将会在 bean 的生命周期章节中进行讨论。 |
| destruction 方法           | 当包含该 bean 的容器被销毁时，使用回调方法。它将会在 bean 的生命周期章节中进行讨论。 |

**Bean 与 Spring 容器的关系**

下图表达了Bean 与 Spring 容器之间的关系：

![Spring Bean 定义](https://7n.w3cschool.cn/attachments/image/20181029/1540796406991057.jpg) 





## 四、Spring Bean作用域：

当在 Spring 中定义一个 bean 时，你必须声明该 bean 的作用域的选项。例如，为了强制 Spring 在每次需要时都产生一个新的 bean 实例，你应该声明 bean 的作用域的属性为 **prototype**。同理，如果你想让 Spring 在每次需要时都返回同一个bean实例，你应该声明 bean 的作用域的属性为 **singleton**。

Spring 框架支持以下五个作用域，分别为singleton、prototype、request、session和global session，5种作用域说明如下所示：    

| 作用域            | 描述                                       |
| -------------- | ---------------------------------------- |
| singleton      | 在spring IoC容器仅存在一个Bean实例，Bean以单例方式存在，默认值 |
| prototype      | 每次从容器中调用Bean时，都返回一个新的实例，即每次调用getBean()时，相当于执行newXxxBean() |
| request        | 每次HTTP请求都会创建一个新的Bean，该作用域仅适用于WebApplicationContext环境 |
| session        | 同一个HTTP Session共享一个Bean，不同Session使用不同的Bean，仅适用于WebApplicationContext环境 |
| global-session | 一般用于Portlet应用环境，该运用域仅适用于WebApplicationContext环境 |

### singleton 作用域：

singleton 是默认的作用域，也就是说，当定义 Bean 时，如果没有指定作用域配置项，则 Bean 的作用域被默认为 singleton。 

当一个bean的作用域为Singleton，那么Spring IoC容器中只会存在一个共享的bean实例，并且所有对bean的请求，只要id与该bean定义相匹配，则只会返回bean的同一实例。

也就是说，当将一个bean定义设置为singleton作用域的时候，Spring IoC容器只会创建该bean定义的唯一实例。

Singleton是单例类型，就是在创建起容器时就同时自动创建了一个bean的对象，不管你是否使用，他都存在了，每次获取到的对象都是同一个对象。注意，Singleton作用域是Spring中的缺省作用域。你可以在 bean 的配置文件中设置作用域的属性为 singleton。

### prototype 作用域

当一个bean的作用域为Prototype，表示一个bean定义对应多个对象实例。Prototype作用域的bean会导致在每次对该bean请求（将其注入到另一个bean中，或者以程序的方式调用容器的getBean()方法）时都会创建一个新的bean实例。Prototype是原型类型，它在我们创建容器的时候并没有实例化，而是当我们获取bean的时候才会去创建一个对象，而且我们每次获取到的对象都不是同一个对象。根据经验，对有状态的bean应该使用prototype作用域，而对无状态的bean则应该使用singleton作用域。



```xml
    <!-- 作用域：singleton + prototype -->
    <bean id="user" class="model.User" scope="singleton">
        <property name="name" value="tony"/>
        <property name="phone" value="13244556677"/>
     </bean>
```

```java


public static void main(String[] args) {
        ClassPathXmlApplicationContext ac = new ClassPathXmlApplicationContext("classpath:spring.xml");

        User user = (User)ac.getBean("user");
        User user2 = (User)ac.getBean("user");

        System.out.println("user == user2 :" + (user == user2));
    }

```

```

结果：
     第一种情况：scope="singleton"时，打印结果：user == user2 :true
     第二种情况：scope="prototype"时，打印结果：user == user2 :false 
```



## 五、(重点)Spring Bean生命周期------四个阶段：

Spring Bean的生命周期只有这四个阶段。把这四个阶段和每个阶段对应的扩展点糅合在一起虽然没有问题，但是这样非常凌乱，难以记忆。要彻底搞清楚Spring的生命周期，首先要把这四个阶段牢牢记住。实例化和属性赋值对应构造方法和setter方法的注入，初始化和销毁是用户能自定义扩展的两个阶段。

- 实例化 Instantiation
- 属性赋值 Populate
- 初始化 Initialization
- 销毁 Destruction

实例化 -> 属性赋值 -> 初始化 -> 销毁

主要逻辑都在doCreate()方法中，逻辑很清晰，就是顺序调用以下三个方法，这三个方法与三个生命周期阶段一一对应，非常重要，在后续扩展接口分析中也会涉及。

- createBeanInstance() -> 实例化
- populateBean() -> 属性赋值
- initializeBean() -> 初始化

```xml
<bean id="people" class="model.People" init-method="init" destroy-method="destroy">
        <property name="name" value="tony2"/>
        <property name="gender" value="man"/>
</bean>
```



```java
public class People {
    private String name;
    private String gender;

    public People() {
        System.out.println("【构造器--People】调用People无参构造器实例化");
    }

    public void init() {
        System.out.println("【初始化--People】调用People的init()初始化");
    }

    public void destroy() {
        System.out.println("【销毁--People】调用People的destroy()销毁");
    }

    public void setName(String name) {
        System.out.println("【注入属性--People】注入属性name");
        this.name = name;
    }

    public void setGender(String gender) {
        System.out.println("【注入属性--People】注入属性gender");
        this.gender = gender;
    }
}
```

```java
public static void main(String[] args) {
        /**
         * Spring Bean 生命周期共四个阶段：实例化 + 属性设置 + 初始化 + 销毁
         */
        ClassPathXmlApplicationContext ac = new ClassPathXmlApplicationContext();
        ac.setConfigLocation("classpath:spring.xml");
        ac.refresh();
        ac.destroy();
    }
```

```xml
打印结果：
      【构造器--People】调用People无参构造器实例化
      【注入属性--People】注入属性name
      【注入属性--People】注入属性gender
      【初始化--People】调用People的init()初始化
      【销毁--People】调用People的destroy()销毁
```



##### 关于在spring  容器初始化 bean 和销毁前所做的操作定义方式有三种：

第一种是：通过 在xml中定义init-method 和  destory-method方法

第二种是： 通过bean实现InitializingBean和 DisposableBean接口

第三种：通过@PostConstruct 和 @PreDestroy 方法 实现初始化和销毁bean之前进行的操作

```xml
<bean class="processor.MyBeanPostProcessor" init-method="init" destroy-method="destroy"/>
```

```java
public class People implements InitializingBean, DisposableBean {
  
  ....
    
  // 初始化时候调用
  public void afterPropertiesSet() {
        System.out.println("【InitializingBean接口调用】InitializingBean.afterPropertiesSet()");
    }
  // bean销毁前调用
  public void destroy() {
        System.out.println("【销毁--People】调用People的destroy()销毁");
    }
}
```

```java
public class People implements InitializingBean, DisposableBean {
  
    ....
   @PostConstruct
    public void  testPostConstruct() {
        System.out.println("@PostConstruct调用");
    }

    @PreDestroy
    public void  testPreDestroy() {
        System.out.println("@PreDestroy调用");
    }
 }
```

```

三种方式打印顺序：

【构造器--People】调用People无参构造器实例化
【注入属性--People】注入属性name
【注入属性--People】注入属性gender
@PostConstruct调用
【InitializingBean接口调用】InitializingBean.afterPropertiesSet()
【初始化--People】调用People的init()初始化
@PreDestroy调用
【DisposableBean接口调用】DisposableBean.destroy()
【销毁--People】调用People的destroy()销毁
```



## 六、Spring Bean后置处理器：

**容器级别，对所有定义在Spring容器中的bean起作用。**

#### (1)BeanPostProcesser：在Bean初始化前后调用，即 Initialization

postProcessBeforeInitialization：初始化前调用

postProcessAfterInitialization： 初始化后调用

```xml
<bean id="people" class="model.People" init-method="init" destroy-method="destroyBean">
        <property name="name" value="tony2"/>
        <property name="gender" value="man"/>
</bean>
<bean class="processor.MyBeanPostProcessor" init-method="init" destroy-method="destroy"/>
```

```java
public class MyBeanPostProcessor implements BeanPostProcessor {

    public MyBeanPostProcessor() {
        System.out.println("【构造器--BeanPostProcessor】调用BeanPostProcessor无参构造器实例化)");
    }

    public void init() {
        System.out.println("【初始化--BeanPostProcessor】调用BeanPostProcessor的init()初始化");
    }

    public void destroy() {
        System.out.println("【销毁--BeanPostProcessor】调用BeanPostProcessor的destroy()销毁");
    }

    public Object postProcessBeforeInitialization(Object bean, String beanName) {
        System.out.println("【方法调用--BeanPostProcessor】调用BeanPostProcessor的postProcessBeforeInitialization()");
        return bean;
    }

    public Object postProcessAfterInitialization(Object bean, String beanName) {
        System.out.println("【方法调用--BeanPostProcessor】调用BeanPostProcessor的postProcessAfterInitialization()");
        return bean;
    }
}
```

```

【构造器--BeanPostProcessor】调用BeanPostProcessor无参构造器实例化
【初始化--BeanPostProcessor】调用BeanPostProcessor的init()初始化
【构造器--People】调用People无参构造器实例化
【注入属性--People】注入属性name
【注入属性--People】注入属性gender
【方法调用--BeanPostProcessor】调用BeanPostProcessor的postProcessBeforeInitialization()
【初始化--People】调用People的init()初始化
【方法调用--BeanPostProcessor】调用BeanPostProcessor的postProcessAfterInitialization()
【销毁--People】调用People的destroy()销毁
【销毁--BeanPostProcessor】调用BeanPostProcessor的destroy()销毁

```



#### (2)InstantiationAwareBeanPostProcessorAdapter：在Bean实例化前后调用，即Instantiation

postProcessBeforeInstantiation: 实例化前调用

postProcessAfterInstantiation：实例化后调用

```xml
<bean id="people" class="model.People" init-method="init" destroy-method="destroyBean">
        <property name="name" value="tony2"/>
        <property name="gender" value="man"/>
</bean>
<bean class="processor.MyInstantiationAwareBeanPostProcessorAdapter" 
      init-method="init" destroy-method="destroy"/>
```

```java
public class MyInstantiationAwareBeanPostProcessorAdapter extends InstantiationAwareBeanPostProcessorAdapter {

    public MyInstantiationAwareBeanPostProcessorAdapter() {
        System.out.println("【构造器--InstantiationAwareBeanPostProcessorAdapter】调用InstantiationAwareBeanPostProcessorAdapter无参构造器实例化)");
    }

    public void init() {
        System.out.println("【初始化--InstantiationAwareBeanPostProcessorAdapter】调用InstantiationAwareBeanPostProcessorAdapter的init()初始化");
    }

    public void destroy() {
        System.out.println("【销毁--InstantiationAwareBeanPostProcessorAdapter】调用InstantiationAwareBeanPostProcessorAdapter的destroy()销毁");
    }

    @Override
    public Object postProcessBeforeInstantiation(Class<?> beanClass, String beanName) throws BeansException {
        System.out.println("【InstantiationAwareBeanPostProcessorAdapter调用】 方法：postProcessBeforeInstantiation()");
        return super.postProcessBeforeInstantiation(beanClass, beanName);
    }

    @Override
    public boolean postProcessAfterInstantiation(Object bean, String beanName) throws BeansException {
        System.out.println("【InstantiationAwareBeanPostProcessorAdapter调用】方法：postProcessAfterInstantiation()");
        return super.postProcessAfterInstantiation(bean, beanName);
    }

    @Override
    public PropertyValues postProcessProperties(PropertyValues pvs, Object bean, String beanName) throws BeansException {
        System.out.println("【InstantiationAwareBeanPostProcessorAdapter调用】方法：postProcessProperties()");
        return super.postProcessProperties(pvs, bean, beanName);
    }
}
```

```

【构造器--InstantiationAwareBeanPostProcessorAdapter】调用无参构造器实例化)
【初始化--InstantiationAwareBeanPostProcessorAdapter】调用init()初始化
【InstantiationAwareBeanPostProcessorAdapter调用】 方法：postProcessBeforeInstantiation()
【构造器--People】调用People无参构造器实例化
【InstantiationAwareBeanPostProcessorAdapter调用】方法：postProcessAfterInstantiation()
【InstantiationAwareBeanPostProcessorAdapter调用】方法：postProcessProperties()
【注入属性--People】注入属性name
【注入属性--People】注入属性gender
【初始化--People】调用People的init()初始化
【销毁--People】调用People的destroy()销毁
【销毁--InstantiationAwareBeanPostProcessorAdapter】调用destroy()销毁
```



#### （3）BeanFactoryPostProcessor: 在所有Bean实例化前调用

```xml
<bean id="people" class="model.People" init-method="init" destroy-method="destroyBean">
        <property name="name" value="tony2"/>
        <property name="gender" value="man"/>
</bean>
<bean class="processor.MyBeanFactoryPostProcessor" init-method="init" destroy-method="destroy"/>
```

```java
public class MyBeanFactoryPostProcessor implements BeanFactoryPostProcessor {

    public MyBeanFactoryPostProcessor() {
        System.out.println("【构造器--BeanFactoryPostProcessor】调用无参构造器实例化");
    }

    public void init() {
        System.out.println("【初始化--BeanFactoryPostProcessor】调用init()初始化");
    }

    public void destroy() {
        System.out.println("【销毁--BeanFactoryPostProcessor】调用destroy()销毁");
    }

    public void postProcessBeanFactory(ConfigurableListableBeanFactory configurableListableBeanFactory) throws BeansException {
        System.out.println("【方法调用--BeanFactoryPostProcessor】调用postProcessBeanFactory()");
    }
}
```

```

【构造器--BeanFactoryPostProcessor】调用无参构造器实例化
【初始化--BeanFactoryPostProcessor】调用init()初始化
【方法调用--BeanFactoryPostProcessor】调用postProcessBeanFactory()
【构造器--People】调用People无参构造器实例化
【注入属性--People】注入属性name
【注入属性--People】注入属性gender
【初始化--People】调用People的init()初始化
【销毁--People】调用People的destroy()销毁
【销毁--BeanFactoryPostProcessor】调用destroy()销毁
```



#### （4）Spring Bean 后置处理器调用顺序打印：

```xml
【构造器--BeanFactoryPostProcessor】调用无参构造器实例化
【初始化--BeanFactoryPostProcessor】调用init()初始化
【方法调用--BeanFactoryPostProcessor】调用postProcessBeanFactory()
【构造器--BeanPostProcessor】调用BeanPostProcessor无参构造器实例化)
【初始化--BeanPostProcessor】调用BeanPostProcessor的init()初始化
【构造器--InstantiationAwareBeanPostProcessorAdapter】调用无参构造器实例化)
【初始化--InstantiationAwareBeanPostProcessorAdapter】调用init()初始化
【InstantiationAwareBeanPostProcessorAdapter调用】 方法：postProcessBeforeInstantiation()
【构造器--People】调用People无参构造器实例化
【InstantiationAwareBeanPostProcessorAdapter调用】方法：postProcessAfterInstantiation()
【InstantiationAwareBeanPostProcessorAdapter调用】方法：postProcessProperties()
【注入属性--People】注入属性name
【注入属性--People】注入属性gender
【方法调用--BeanPostProcessor】调用BeanPostProcessor的postProcessBeforeInitialization()
@PostConstruct调用
【InitializingBean接口调用】InitializingBean.afterPropertiesSet()
【初始化--People】调用People的init()初始化
【方法调用--BeanPostProcessor】调用BeanPostProcessor的postProcessAfterInitialization()
@PreDestroy调用
【DisposableBean接口调用】DisposableBean.destroy()
【销毁--People】调用People的destroy()销毁
【销毁--InstantiationAwareBeanPostProcessorAdapter】调用destroy()销毁
【销毁--BeanPostProcessor】调用BeanPostProcessor的destroy()销毁
【销毁--BeanFactoryPostProcessor】调用destroy()销毁
```

![springBean生命周期图](E:\熙邻\技术分享\2019.08-spring\img\springBean生命周期图.png)



#### （5） 后置处理器在Spring底层应用：



## 七、源码分析：

IOC容器启动过程
资源定位(classpath,filesystem等)
载入，将bean定义转换为内部数据结构BeanDefintion
向IOC容器注册bean
资源定位
定位配置文件，通过BeadDefinetionReader读入配置

refresh方法实现
IOC容器启动方法是ConfigurableApplicationContext中定义的refresh方法，在AbstractApplicationContext中实现

//准备阶段做的是：标记容器为active状态，以及检查当前的运行环境，比如使用log4j，还是jdklog等
prepareRefresh();

//创建beanfactory， 定位资源，载入，装配beandefinetion
// Tell the subclass to refresh the internal bean factory.
ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();

//初始化beanfactory, 设置contex的classloader

// Prepare the bean factory for use in this context.
prepareBeanFactory(beanFactory);

try {
    //在基本的初始化之后，对beanfactory做一些修改
    // Allows post-processing of the bean factory in context subclasses.
    postProcessBeanFactory(beanFactory);
    
    //调用beanfactory后处理器
    // Invoke factory processors registered as beans in the context.
    invokeBeanFactoryPostProcessors(beanFactory);
    
    //注册bean后处理器
    // Register bean processors that intercept bean creation.
    registerBeanPostProcessors(beanFactory);
    
    //初始化消息资源，国际化
    // Initialize message source for this context.
    initMessageSource();
    
    // Initialize event multicaster for this context.
    initApplicationEventMulticaster();
    //在实例化bean之前，做一些工作
    // Initialize other special beans in specific context subclasses.
    onRefresh();
    
    //注册 ApplicationListener监听器
    // Check for listener beans and register them.
    registerListeners();
    
    //实例化bean
    // Instantiate all remaining (non-lazy-init) singletons.
    finishBeanFactoryInitialization(beanFactory);
    
    //发布bean正常响应事件，初始化bean生命周期处理器
    // Last step: publish corresponding event.
    finishRefresh();








Spring容器的refresh()【创建刷新】;
1、prepareRefresh()刷新前的预处理;

	1）、initPropertySources()初始化一些属性设置;子类自定义个性化的属性设置方法；
	2）、getEnvironment().validateRequiredProperties();检验属性的合法等
	3）、earlyApplicationEvents= new LinkedHashSet<ApplicationEvent>();保存容器中的一些早期的事件；

2、obtainFreshBeanFactory();获取BeanFactory；
	1）、refreshBeanFactory();刷新【创建】BeanFactory；
			创建了一个this.beanFactory = new DefaultListableBeanFactory();
			设置id；
	2）、getBeanFactory();返回刚才GenericApplicationContext创建的BeanFactory对象；
	3）、将创建的BeanFactory【DefaultListableBeanFactory】返回；

3、prepareBeanFactory(beanFactory);BeanFactory的预准备工作（BeanFactory进行一些设置）；
	1）、设置BeanFactory的类加载器、支持表达式解析器...
	2）、添加部分BeanPostProcessor【ApplicationContextAwareProcessor】
	3）、设置忽略的自动装配的接口EnvironmentAware、EmbeddedValueResolverAware、xxx；
	4）、注册可以解析的自动装配；我们能直接在任何组件中自动注入：
			BeanFactory、ResourceLoader、ApplicationEventPublisher、ApplicationContext
	5）、添加BeanPostProcessor【ApplicationListenerDetector】
	6）、添加编译时的AspectJ；
	7）、给BeanFactory中注册一些能用的组件；
		environment【ConfigurableEnvironment】、
		systemProperties【Map<String, Object>】、
		systemEnvironment【Map<String, Object>】

4、postProcessBeanFactory(beanFactory);BeanFactory准备工作完成后进行的后置处理工作；
	1）、子类通过重写这个方法来在BeanFactory创建并预准备完成以后做进一步的设置

======================以上是BeanFactory的创建及预准备工作==================================
5、invokeBeanFactoryPostProcessors(beanFactory);执行BeanFactoryPostProcessor的方法；
	BeanFactoryPostProcessor：BeanFactory的后置处理器。在BeanFactory标准初始化之后执行的；
	两个接口：BeanFactoryPostProcessor、BeanDefinitionRegistryPostProcessor
		先执行BeanDefinitionRegistryPostProcessor
		1）、获取所有的BeanDefinitionRegistryPostProcessor；
		2）、看先执行实现了PriorityOrdered优先级接口的BeanDefinitionRegistryPostProcessor、
			postProcessor.postProcessBeanDefinitionRegistry(registry)
		3）、在执行实现了Ordered顺序接口的BeanDefinitionRegistryPostProcessor；
			postProcessor.postProcessBeanDefinitionRegistry(registry)
		4）、最后执行没有实现任何优先级或者是顺序接口的BeanDefinitionRegistryPostProcessors；
			postProcessor.postProcessBeanDefinitionRegistry(registry)


		再执行BeanFactoryPostProcessor的方法
		1）、获取所有的BeanFactoryPostProcessor
		2）、看先执行实现了PriorityOrdered优先级接口的BeanFactoryPostProcessor、
			postProcessor.postProcessBeanFactory()
		3）、在执行实现了Ordered顺序接口的BeanFactoryPostProcessor；
			postProcessor.postProcessBeanFactory()
		4）、最后执行没有实现任何优先级或者是顺序接口的BeanFactoryPostProcessor；
			postProcessor.postProcessBeanFactory()

6、registerBeanPostProcessors(beanFactory);注册BeanPostProcessor（Bean的后置处理器）【 intercept bean creation】
		不同接口类型的BeanPostProcessor；在Bean创建前后的执行时机是不一样的
		BeanPostProcessor、
		DestructionAwareBeanPostProcessor、
		InstantiationAwareBeanPostProcessor、
		SmartInstantiationAwareBeanPostProcessor、
		MergedBeanDefinitionPostProcessor【internalPostProcessors】、
		
		1）、获取所有的 BeanPostProcessor;后置处理器都默认可以通过PriorityOrdered、Ordered接口来执行优先级
		2）、先注册PriorityOrdered优先级接口的BeanPostProcessor；
			把每一个BeanPostProcessor；添加到BeanFactory中
			beanFactory.addBeanPostProcessor(postProcessor);
		3）、再注册Ordered接口的
		4）、最后注册没有实现任何优先级接口的
		5）、最终注册MergedBeanDefinitionPostProcessor；
		6）、注册一个ApplicationListenerDetector；来在Bean创建完成后检查是否是ApplicationListener，如果是
			applicationContext.addApplicationListener((ApplicationListener<?>) bean);

7、initMessageSource();初始化MessageSource组件（做国际化功能；消息绑定，消息解析）；
		1）、获取BeanFactory
		2）、看容器中是否有id为messageSource的，类型是MessageSource的组件
			如果有赋值给messageSource，如果没有自己创建一个DelegatingMessageSource；
				MessageSource：取出国际化配置文件中的某个key的值；能按照区域信息获取；
		3）、把创建好的MessageSource注册在容器中，以后获取国际化配置文件的值的时候，可以自动注入MessageSource；
			beanFactory.registerSingleton(MESSAGE_SOURCE_BEAN_NAME, this.messageSource);	
			MessageSource.getMessage(String code, Object[] args, String defaultMessage, Locale locale);
            
8、initApplicationEventMulticaster();初始化事件派发器；
		1）、获取BeanFactory
		2）、从BeanFactory中获取applicationEventMulticaster的ApplicationEventMulticaster；
		3）、如果上一步没有配置；创建一个SimpleApplicationEventMulticaster
		4）、将创建的ApplicationEventMulticaster添加到BeanFactory中，以后其他组件直接自动注入
9、onRefresh();留给子容器（子类）
		1、子类重写这个方法，在容器刷新的时候可以自定义逻辑；
10、registerListeners();给容器中将所有项目里面的ApplicationListener注册进来；
		1、从容器中拿到所有的ApplicationListener
		2、将每个监听器添加到事件派发器中；
			getApplicationEventMulticaster().addApplicationListenerBean(listenerBeanName);
		3、派发之前步骤产生的事件；
11、finishBeanFactoryInitialization(beanFactory);初始化所有剩下的单实例bean；
	1、beanFactory.preInstantiateSingletons();初始化后剩下的单实例bean
		1）、获取容器中的所有Bean，依次进行初始化和创建对象
		2）、获取Bean的定义信息；RootBeanDefinition
		3）、Bean不是抽象的，是单实例的，是懒加载；
			1）、判断是否是FactoryBean；是否是实现FactoryBean接口的Bean；
			2）、不是工厂Bean。利用getBean(beanName);创建对象
				0、getBean(beanName)； ioc.getBean();
				1、doGetBean(name, null, null, false);
				2、先获取缓存中保存的单实例Bean。如果能获取到说明这个Bean之前被创建过（所有创建过的单实例Bean都会被缓存起来）
					从private final Map<String, Object> singletonObjects = new ConcurrentHashMap<String, Object>(256);获取的
				3、缓存中获取不到，开始Bean的创建对象流程；
				4、标记当前bean已经被创建
				5、获取Bean的定义信息；
				6、【获取当前Bean依赖的其他Bean;如果有按照getBean()把依赖的Bean先创建出来；】
				7、启动单实例Bean的创建流程；
					1）、createBean(beanName, mbd, args);
					2）、Object bean = resolveBeforeInstantiation(beanName, mbdToUse);让BeanPostProcessor先拦截返回代理对象；
						【InstantiationAwareBeanPostProcessor】：提前执行；
						先触发：postProcessBeforeInstantiation()；
						如果有返回值：触发postProcessAfterInitialization()；
					3）、如果前面的InstantiationAwareBeanPostProcessor没有返回代理对象；调用4）
					4）、Object beanInstance = doCreateBean(beanName, mbdToUse, args);创建Bean
						 1）、【创建Bean实例】；createBeanInstance(beanName, mbd, args);
						 	利用工厂方法或者对象的构造器创建出Bean实例；
						 2）、applyMergedBeanDefinitionPostProcessors(mbd, beanType, beanName);
						 	调用MergedBeanDefinitionPostProcessor的postProcessMergedBeanDefinition(mbd, beanType, beanName);
						 3）、【Bean属性赋值】populateBean(beanName, mbd, instanceWrapper);
						 	赋值之前：
						 	1）、拿到InstantiationAwareBeanPostProcessor后置处理器；
						 		postProcessAfterInstantiation()；
						 	2）、拿到InstantiationAwareBeanPostProcessor后置处理器；
						 		postProcessPropertyValues()；
						 	=====赋值之前：===
						 	3）、应用Bean属性的值；为属性利用setter方法等进行赋值；
						 		applyPropertyValues(beanName, mbd, bw, pvs);
						 4）、【Bean初始化】initializeBean(beanName, exposedObject, mbd);
						 	1）、【执行Aware接口方法】invokeAwareMethods(beanName, bean);执行xxxAware接口的方法
						 		BeanNameAware\BeanClassLoaderAware\BeanFactoryAware
						 	2）、【执行后置处理器初始化之前】applyBeanPostProcessorsBeforeInitialization(wrappedBean, beanName);
						 		BeanPostProcessor.postProcessBeforeInitialization（）;
						 	3）、【执行初始化方法】invokeInitMethods(beanName, wrappedBean, mbd);
						 		1）、是否是InitializingBean接口的实现；执行接口规定的初始化；
						 		2）、是否自定义初始化方法；
						 	4）、【执行后置处理器初始化之后】applyBeanPostProcessorsAfterInitialization
						 		BeanPostProcessor.postProcessAfterInitialization()；
						 5）、注册Bean的销毁方法；
					5）、将创建的Bean添加到缓存中singletonObjects；
				ioc容器就是这些Map；很多的Map里面保存了单实例Bean，环境信息。。。。；
		所有Bean都利用getBean创建完成以后；
			检查所有的Bean是否是SmartInitializingSingleton接口的；如果是；就执行afterSingletonsInstantiated()；
12、finishRefresh();完成BeanFactory的初始化创建工作；IOC容器就创建完成；
		1）、initLifecycleProcessor();初始化和生命周期有关的后置处理器；LifecycleProcessor
			默认从容器中找是否有lifecycleProcessor的组件【LifecycleProcessor】；如果没有new DefaultLifecycleProcessor();
			加入到容器；
			
			写一个LifecycleProcessor的实现类，可以在BeanFactory
				void onRefresh();
				void onClose();	
		2）、	getLifecycleProcessor().onRefresh();
			拿到前面定义的生命周期处理器（BeanFactory）；回调onRefresh()；
		3）、publishEvent(new ContextRefreshedEvent(this));发布容器刷新完成事件；
		4）、liveBeansView.registerApplicationContext(this);
	
	======总结===========
	1）、Spring容器在启动的时候，先会保存所有注册进来的Bean的定义信息；
		1）、xml注册bean；<bean>
		2）、注解注册Bean；@Service、@Component、@Bean、xxx
	2）、Spring容器会合适的时机创建这些Bean
		1）、用到这个bean的时候；利用getBean创建bean；创建好以后保存在容器中；
		2）、统一创建剩下所有的bean的时候；finishBeanFactoryInitialization()；
	3）、后置处理器；BeanPostProcessor
		1）、每一个bean创建完成，都会使用各种后置处理器进行处理；来增强bean的功能；
			AutowiredAnnotationBeanPostProcessor:处理自动注入
			AnnotationAwareAspectJAutoProxyCreator:来做AOP功能；
			xxx....
			增强的功能注解：
			AsyncAnnotationBeanPostProcessor
			....
	4）、事件驱动模型；
		ApplicationListener；事件监听；
		ApplicationEventMulticaster；事件派发：


​					
​						 		
​						 	
​					
​								
​							
​							
​							
​						
​						
​						
​							
​						
​						
​					
​				
​				
​				
​			


​	
​	