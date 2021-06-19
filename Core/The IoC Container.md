# Core Technologies (核心技术)

> Version 5.3.8
>
> [TOC]
>
> 

This part of the reference documentation covers all the technologies that are absolutely integral to the Spring Framework.

参考文档的这一部分涵盖了Spring框架中绝对不可或缺的所有技术。

Foremost amongst these is the Spring Framework’s Inversion of Control (IoC) container. A thorough treatment of the Spring Framework’s IoC container is closely followed by comprehensive coverage of Spring’s Aspect-Oriented Programming (AOP) technologies. The Spring Framework has its own AOP framework, which is conceptually easy to understand and which successfully addresses the 80% sweet spot of AOP requirements in Java enterprise programming.

其中最重要的是Spring框架的控制反转(IoC)容器。在对Spring框架的IoC容器进行了全面的处理之后，紧接着全面介绍了Spring的面向方面编程(AOP)技术。Spring框架有自己的AOP框架，它在概念上很容易理解，并且成功地解决了Java企业编程中80%的AOP需求。

Coverage of Spring’s integration with AspectJ (currently the richest — in terms of features — and certainly most mature AOP implementation in the Java enterprise space) is also provided.

还介绍了Spring与AspectJ的集成(就特性而言，目前AspectJ是最丰富的，当然也是Java企业空间中最成熟的AOP实现)。

## 1. The IoC Container (IoC容器)

This chapter covers Spring’s Inversion of Control (IoC) container.

本章介绍Spring的控制反转(IoC)容器。

### 1.1. Introduction to the Spring IoC Container and Beans (Spring IoC容器和bean的介绍)

This chapter covers the Spring Framework implementation of the Inversion of Control (IoC) principle. IoC is also known as dependency injection (DI). It is a process whereby objects define their dependencies (that is, the other objects they work with) only through constructor arguments, arguments to a factory method, or properties that are set on the object instance after it is constructed or returned from a factory method. The container then injects those dependencies when it creates the bean. This process is fundamentally the inverse (hence the name, Inversion of Control) of the bean itself controlling the instantiation or location of its dependencies by using direct construction of classes or a mechanism such as the Service Locator pattern.

本章介绍了控制反转(IoC)原理的Spring框架实现。IoC也称为依赖项注入(DI)。在这个过程中，对象只能通过构造函数参数、工厂方法的参数或在对象实例被构造或从工厂方法返回后设置的属性来定义它们的依赖关系(即它们使用的其他对象)。然后，容器在创建bean时注入这些依赖项。这个过程基本上是bean本身通过直接构造类或诸如Service Locator模式这样的机制来控制其依赖项的实例化或位置的逆过程(因此得名“控制反转”)。

The `org.springframework.beans` and `org.springframework.context` packages are the basis for Spring Framework’s IoC container. The [`BeanFactory`](https://docs.spring.io/spring-framework/docs/5.3.8/javadoc-api/org/springframework/beans/factory/BeanFactory.html) interface provides an advanced configuration mechanism capable of managing any type of object. [`ApplicationContext`](https://docs.spring.io/spring-framework/docs/5.3.8/javadoc-api/org/springframework/context/ApplicationContext.html) is a sub-interface of `BeanFactory`. It adds:

- Easier integration with Spring’s AOP features
- Message resource handling (for use in internationalization)
- Event publication
- Application-layer specific contexts such as the `WebApplicationContext` for use in web applications.

`org.springframework.beans` 和 `org.springframework.context` 包是Spring框架的IoC容器的基础。[`BeanFactory`](https://docs.spring.io/spring-framework/docs/5.3.8/javadoc-api/org/springframework/beans/factory/BeanFactory.html) 接口提供了能够管理任何类型对象的高级配置机制。[`ApplicationContext`](https://docs.spring.io/spring-framework/docs/5.3.8/javadoc-api/org/springframework/context/ApplicationContext.html) 是 `BeanFactory` 的一个子接口。它补充了:

- 更容易与Spring的AOP特性集成
- 消息资源处理(用于国际化)
- 事件发布
- 应用层特定的上下文，如在web应用程序中使用的 `WebApplicationContext`

In short, the `BeanFactory` provides the configuration framework and basic functionality, and the `ApplicationContext` adds more enterprise-specific functionality. The `ApplicationContext` is a complete superset of the `BeanFactory` and is used exclusively in this chapter in descriptions of Spring’s IoC container. For more information on using the `BeanFactory` instead of the `ApplicationContext,` see [The `BeanFactory`](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-beanfactory).

简而言之，`BeanFactory` 提供了配置框架和基本功能，而 `ApplicationContext` 增加了更多特定于企业的功能。`ApplicationContext` 是 `BeanFactory` 的一个完整的超集，在本章描述Spring的IoC容器时专门使用。
有关使用 `BeanFactory ` 而不是 `ApplicationContext` 的更多信息，请参见 `BeanFactory`。

In Spring, the objects that form the backbone of your application and that are managed by the Spring IoC container are called beans. A bean is an object that is instantiated, assembled, and managed by a Spring IoC container. Otherwise, a bean is simply one of many objects in your application. Beans, and the dependencies among them, are reflected in the configuration metadata used by a container.

在Spring中，构成应用程序主干并由Spring IoC容器管理的对象称为bean。bean是由Spring IoC容器实例化、组装和管理的对象。否则，bean只是应用程序中的众多对象之一。bean及其之间的依赖关系反映在容器使用的配置元数据中。

### 1.2. Container Overview (容器概述)

The `org.springframework.context.ApplicationContext` interface represents the Spring IoC container and is responsible for instantiating, configuring, and assembling the beans. The container gets its instructions on what objects to instantiate, configure, and assemble by reading configuration metadata. The configuration metadata is represented in XML, Java annotations, or Java code. It lets you express the objects that compose your application and the rich interdependencies between those objects.

`org.springframework.context.ApplicationContext` 接口代表Spring IoC容器，负责实例化、配置和组装bean。容器通过读取配置元数据获得关于实例化、配置和组装哪些对象的指令。配置元数据用XML、Java注释或Java代码表示。它允许您表达组成应用程序的对象以及这些对象之间丰富的相互依赖关系。

Several implementations of the `ApplicationContext` interface are supplied with Spring. In stand-alone applications, it is common to create an instance of [`ClassPathXmlApplicationContext`](https://docs.spring.io/spring-framework/docs/5.3.8/javadoc-api/org/springframework/context/support/ClassPathXmlApplicationContext.html) or [`FileSystemXmlApplicationContext`](https://docs.spring.io/spring-framework/docs/5.3.8/javadoc-api/org/springframework/context/support/FileSystemXmlApplicationContext.html). While XML has been the traditional format for defining configuration metadata, you can instruct the container to use Java annotations or code as the metadata format by providing a small amount of XML configuration to declaratively enable support for these additional metadata formats.

Spring提供了  `org.springframework.context.ApplicationContext` 接口的几个实现。在独立应用程序中，通常会创建 `ClassPathXmlApplicationContext` 或 `FileSystemXmlApplicationContext` 的实例。
虽然XML是定义配置元数据的传统格式，但是您可以通过提供少量XML配置以声明支持这些额外的元数据格式，从而指示容器使用Java注解或代码作为元数据格式。

In most application scenarios, explicit user code is not required to instantiate one or more instances of a Spring IoC container. For example, in a web application scenario, a simple eight (or so) lines of boilerplate web descriptor XML in the `web.xml` file of the application typically suffices (see [Convenient ApplicationContext Instantiation for Web Applications](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#context-create)). If you use the [Spring Tools for Eclipse](https://spring.io/tools) (an Eclipse-powered development environment), you can easily create this boilerplate configuration with a few mouse clicks or keystrokes.

在大多数应用场景中，不需要显式的用户代码来实例化一个或多个 Spring IoC 容器实例。例如，在 Web 应用程序场景中，在应用程序的web.xml文件中简单的8行(大约)样板Web描述符XML就足够了（请参阅[Web 应用程序的便捷 ApplicationContext 实例化](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#context-create)）。如果您使用 [Spring Tools for Eclipse](https://spring.io/tools)（一个 Eclipse 驱动的开发环境），您可以通过点击几下鼠标或按键轻松创建这个样板配置。

The following diagram shows a high-level view of how Spring works. Your application classes are combined with configuration metadata so that, after the `ApplicationContext` is created and initialized, you have a fully configured and executable system or application.

下图高度概括展示了Spring是如何工作的。你的应用程序中的类通过配置元数据从而整合到一起，这样，在 `ApplicationContext` 创建并初始化之后，你就会有一个配置好的可执行的系统或应用程序。

![container magic](https://docs.spring.io/spring-framework/docs/current/reference/html/images/container-magic.png)

**Figure 1. The Spring IoC container**

As the preceding diagram shows, the Spring IoC container consumes a form of configuration metadata. This configuration metadata represents how you, as an application developer, tell the Spring container to instantiate, configure, and assemble the objects in your application.

如上图所示，Spring IoC容器使用配置元数据的形式。这些配置元数据表示作为应用程序开发人员，你如何告诉Spring容器实例化、配置和组装应用程序中的对象。

[^此配置元数据表示作为应用程序开发人员，你如何告诉Spring容器实例化、配置和组装应用程序中的对象。]: 这句话的意思应该是: 你可以通过这些配置来告诉Ioc容器去实例化、配置和组装应用程序中的对象

>  XML-based metadata is not the only allowed form of configuration metadata. The Spring IoC container itself is totally decoupled from the format in which this configuration metadata is actually written. These days, many developers choose [Java-based configuration](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-java) for their Spring applications.
>
> 基于XML的元数据并不是唯一允许的配置元数据形式。Spring IoC容器本身与配置元数据实际编写的格式完全分离。如今许多开发人员在Spring应用程序选择基于Java的配置。

For information about using other forms of metadata with the Spring container, see:

- [Annotation-based configuration](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-annotation-config): Spring 2.5 introduced support for annotation-based configuration metadata.
- [Java-based configuration](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-java): Starting with Spring 3.0, many features provided by the Spring JavaConfig project became part of the core Spring Framework. Thus, you can define beans external to your application classes by using Java rather than XML files. To use these new features, see the [`@Configuration`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Configuration.html), [`@Bean`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Bean.html), [`@Import`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Import.html), and [`@DependsOn`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/DependsOn.html) annotations.



有关在 Spring 容器中使用其他形式的元数据的信息，请参阅：

- [基于注解的配置](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-annotation-config)：Spring 2.5 引入了对基于注解的配置元数据的支持。
- [基于 Java 的配置](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-java)：从 Spring 3.0 开始，Spring JavaConfig 项目提供的许多特性成为核心 Spring Framework 的一部分。因此，您可以使用 Java 而不是 XML 文件来定义应用程序类外部的 bean。要使用这些新功能，请参阅 [`@Configuration`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Configuration.html)， [`@Bean`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Bean.html)， [`@Import`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Import.html)，和[`@DependsOn`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/DependsOn.html)注释。

Spring 配置包含至少一个并且通常不止一个容器必须管理的 bean 定义。基于 XML 的配置元数据将这些 bean 配置为`<beans/>` 这个最顶级元素中的 `<bean/>` 元素中, Java配置方式通常在 `@Configuration` 修饰的类中使用`@bean`注解的方式来进行配置。

These bean definitions correspond to the actual objects that make up your application. Typically, you define service layer objects, data access objects (DAOs), presentation objects such as Struts `Action` instances, infrastructure objects such as Hibernate `SessionFactories`, JMS `Queues`, and so forth. Typically, one does not configure fine-grained domain objects in the container, because it is usually the responsibility of DAOs and business logic to create and load domain objects. However, you can use Spring’s integration with AspectJ to configure objects that have been created outside the control of an IoC container. See [Using AspectJ to dependency-inject domain objects with Spring](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#aop-atconfigurable).

这些bean定义对应于组成应用程序的实际对象。通常，您需要定义服务层对象、数据访问层对象(DAOs)、表现层对象(如Struts Action实例)、基础结构对象(如Hibernate SessionFactories、JMS队列)等等。通常，不需要在容器中配置细粒度的域对象，因为创建和加载域对象通常是DAO层和业务逻辑的责任。但是，您可以使用Spring与AspectJ的集成来配置在IoC容器控制之外创建的对象。请参阅 [使用AspectJ使用Spring依赖注入域对象]((https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#aop-atconfigurable))。

The following example shows the basic structure of XML-based configuration metadata:

下面的例子展示了基于XML的配置元数据的基本结构:

```mxml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="..." class="...">  
        <!-- collaborators and configuration for this bean go here -->
    </bean>

    <bean id="..." class="...">
        <!-- collaborators and configuration for this bean go here -->
    </bean>

    <!-- more bean definitions go here -->

</beans>
```

