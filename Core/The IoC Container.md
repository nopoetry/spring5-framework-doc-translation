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
- The id attribute is a string that identifies the individual bean definition.
- The class attribute defines the type of the bean and uses the fully qualified classname.

The value of the `id` attribute refers to collaborating objects. The XML for referring to collaborating objects is not shown in this example. See [Dependencies](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-dependencies) for more information.

1. `id` 属性是一个字符串, 用来标识单个bean定义
2. `class` 属性使用全类名的方式定义一个bean的类型。

id属性的值引用协作对象。此示例中未显示用于引用协作对象的 XML。有关更多信息，请参见Dependencies。

#### 1.2.2. Instantiating a Container (实例化一个容器)

The location path or paths supplied to an `ApplicationContext` constructor are resource strings that let the container load configuration metadata from a variety of external resources, such as the local file system, the Java `CLASSPATH`, and so on.

提供给 `ApplicationContext` 构造函数的一个或多个位置路径是资源字符串，允许容器从各种外部资源（例如本地文件系统、Java CLASSPATH等）加载配置元数据。

```java
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");
```

>  After you learn about Spring’s IoC container, you may want to know more about Spring’s `Resource` abstraction (as described in [Resources](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#resources)), which provides a convenient mechanism for reading an InputStream from locations defined in a URI syntax. In particular, `Resource` paths are used to construct applications contexts, as described in [Application Contexts and Resource Paths](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#resources-app-ctx).
>
>  在了解了 Spring 的 IoC 容器之后，您可能想了解更多关于 Spring 的 `Resource`抽象（如[参考资料中所述](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#resources)），它提供了一种从 URI 语法中定义的位置读取 InputStream 的便捷机制。特别是， `Resource`路径用于构造应用程序上下文，如[应用程序上下文和资源路径中所述](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#resources-app-ctx)。

The following example shows the service layer objects `(services.xml)` configuration file:

以下示例显示了服务层对象`(services.xml)`配置文件：

```mxml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- services -->

    <bean id="petStore" class="org.springframework.samples.jpetstore.services.PetStoreServiceImpl">
        <property name="accountDao" ref="accountDao"/>
        <property name="itemDao" ref="itemDao"/>
        <!-- additional collaborators and configuration for this bean go here -->
    </bean>

    <!-- more bean definitions for services go here -->

</beans>
```

In the preceding example, the service layer consists of the `PetStoreServiceImpl` class and two data access objects of the types `JpaAccountDao` and `JpaItemDao` (based on the JPA Object-Relational Mapping standard). The `property name` element refers to the name of the JavaBean property, and the `ref` element refers to the name of another bean definition. This linkage between `id` and `ref` elements expresses the dependency between collaborating objects. For details of configuring an object’s dependencies, see [Dependencies](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-dependencies).

在前面的示例中，服务层由 `PetStoreServiceImpl` 类和两个类型 `JpaAccountDao` 和 `JpaItemDao`（基于 JPA 对象-关系映射标准）的数据访问对象组成。该 `property name` 元素是指 JavaBean 属性的名称，以及 `ref` 元素指的是另一个bean定义的名称。(就是id名称)`id` 和 `ref` 元素之间的这种联系表达了协作对象之间的依赖关系。有关配置对象依赖项的详细信息，请参阅 [Dependencies](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-dependencies).

#####  Composing XML-based Configuration Metadata (编写基于XML的配置元数据)

It can be useful to have bean definitions span multiple XML files. Often, each individual XML configuration file represents a logical layer or module in your architecture.

You can use the application context constructor to load bean definitions from all these XML fragments. This constructor takes multiple `Resource` locations, as was shown in the [previous section](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-instantiation). Alternatively, use one or more occurrences of the `<import/>` element to load bean definitions from another file or files. The following example shows how to do so:

让 bean 定义跨越多个 XML 文件会很有用。通常，每个单独的 XML 配置文件都代表您架构中的一个逻辑层或模块。

您可以使用应用程序上下文构造函数从所有这些 XML 片段加载 bean 定义。该构造函数采用多个 `Resource` 位置，如[上一节](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-instantiation)所示 。或者，使用一个或多个 `<import/>` 元素从另一个文件或多个文件加载 bean 定义。以下示例显示了如何执行此操作：

```xml
<beans>
    <import resource="services.xml"/>
    <import resource="resources/messageSource.xml"/>
    <import resource="/resources/themeSource.xml"/>

    <bean id="bean1" class="..."/>
    <bean id="bean2" class="..."/>
</beans>
```

In the preceding example, external bean definitions are loaded from three files: `services.xml`, `messageSource.xml`, and `themeSource.xml`. All location paths are relative to the definition file doing the importing, so `services.xml` must be in the same directory or classpath location as the file doing the importing, while `messageSource.xml` and `themeSource.xml` must be in a `resources` location below the location of the importing file. As you can see, a leading slash is ignored. However, given that these paths are relative, it is better form not to use the slash at all. The contents of the files being imported, including the top level `<beans/>` element, must be valid XML bean definitions, according to the Spring Schema.

在前面的例子中，外部bean定义是从这三个文件中加载： `services.xml`，`messageSource.xml`，和`themeSource.xml`。所有位置路径都相对于执行导入的定义文件(使用的是相对路径)，因此 `services.xml` 必须与执行导入的文件位于同一目录或类路径 (classpath) 位置，而 `messageSource.xml` 和 `themeSource.xml` 必须位于导入文件所在位置下方的 `resources` 位置。如您所见，前导斜杠(相对路径前的/)是被忽略的。但是，鉴于这些路径是相对的，最好根本不使用斜杠。根据 Spring Schema，被导入文件的内容，包括顶级元素`<beans/>`，必须是有效的 XML bean 定义。

>  It is possible, but not recommended, to reference files in parent directories using a relative "../" path. Doing so creates a dependency on a file that is outside the current application. In particular, this reference is not recommended for `classpath:` URLs (for example, `classpath:../services.xml`), where the runtime resolution process chooses the “nearest” classpath root and then looks into its parent directory. Classpath configuration changes may lead to the choice of a different, incorrect directory.
>
> You can always use fully qualified resource locations instead of relative paths: for example, `file:C:/config/services.xml` or `classpath:/config/services.xml`. However, be aware that you are coupling your application’s configuration to specific absolute locations. It is generally preferable to keep an indirection for such absolute locations — for example, through "${…}" placeholders that are resolved against JVM system properties at runtime.
>
>  可以但不建议使用相对 “../” 路径引用父目录中的文件。这样做会为当前应用程序创建一个外部文件的依赖。特别是，不建议将此引用用于`classpath:`URL（例如，`classpath:../services.xml`）, 在这些URL中, 在解析时会选择“最近的” classpath根路径，然后查看其父目录。类路径配置更改可能会导致选择其他不正确的目录。
>
> 您始终可以使用绝对资源路径而不是相对路径：例如，`file:C:/config/services.xml`或`classpath:/config/services.xml`。但是，请意识到, 你正在将应用程序的配置与特定的绝对路径耦合。通常最好为这样的绝对路径保留一个间接地址——例如，使用“${… }”占位符, 在运行时根据 JVM 系统属性解析它。

The namespace itself provides the import directive feature. Further configuration features beyond plain bean definitions are available in a selection of XML namespaces provided by Spring — for example, the `context` and `util` namespaces.

命名空间本身提供了 `import` 指令功能。除了普通bean定义之外，Spring提供的XML名称空间中还提供了更多的配置特性——例如，`context` 和 `util` 命名空间。

##### The Groovy Bean Definition DSL

As a further example for externalized configuration metadata, bean definitions can also be expressed in Spring’s Groovy Bean Definition DSL, as known from the Grails framework. Typically, such configuration live in a ".groovy" file with the structure shown in the following example:

作为外部化配置元数据的另一个示例，Bean定义也可以在Spring的Groovy Bean定义DSL中表达，这从Grails框架中可以知道。通常，此类配置位于一个 ".groovy"文件中，其结构如下例所示：

```groovy
beans {  
 dataSource(BasicDataSource) {  
 driverClassName = "org.hsqldb.jdbcDriver"  
 url = "jdbc:hsqldb:mem:grailsDB"  
 username = "sa"  
 password = ""  
 settings = \[mynew:"setting"\]  
 }  
 sessionFactory(SessionFactory) {  
 dataSource = dataSource  
 }  
 myService(MyService) {  
 nestedBean = { AnotherBean bean ->  
 dataSource = dataSource  
 }  
 }  
}
```

This configuration style is largely equivalent to XML bean definitions and even supports Spring’s XML configuration namespaces. It also allows for importing XML bean definition files through an `importBeans` directive.

这种配置风格在很大程度上等同于XML bean定义，甚至支持Spring的XML配置命名空间。它还允许通过`importBeans` 指令导入XML bean定义文件。

#### 1.2.3. Using the Container

The `ApplicationContext` is the interface for an advanced factory capable of maintaining a registry of different beans and their dependencies. By using the method `T getBean(String name, Class<T> requiredType)`, you can retrieve instances of your beans.

The `ApplicationContext` lets you read bean definitions and access them, as the following example shows:

`ApplicationContext` 是一个高级工厂的接口，能够维护不同 bean 及其依赖项的注册表。通过使用 方法 `T getBean(String name, Class<T> requiredType)`，您可以检索 bean 的实例。

将 `ApplicationContext` 允许你读bean定义和访问它们，如下例所示：

```java
// create and configure beans
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");

// retrieve configured instance
PetStoreService service = context.getBean("petStore", PetStoreService.class);

// use configured instance
List<String> userList = service.getUsernameList();
```

With Groovy configuration, bootstrapping looks very similar. It has a different context implementation class which is Groovy-aware (but also understands XML bean definitions). The following example shows Groovy configuration:

对于Groovy配置来说，它的引导方式看起来和我们之前接触的其他同类引导非常相似。它有一个与众不同且专为Groovy而设计的上下文实现类（该类同样也可以识别XML中的bean定义）。Groovy配置示例如下：

```java
ApplicationContext context = new GenericGroovyApplicationContext("services.groovy", "daos.groovy");
```

The most flexible variant is `GenericApplicationContext` in combination with reader delegates — for example, with `XmlBeanDefinitionReader` for XML files, as the following example shows:

最灵活的变体是 `GenericApplicationContext` 与 reader 委托结合使用——例如，XML文件使用`XmlBeanDefinitionReader`，如下例所示:

```java
GenericApplicationContext context = new GenericApplicationContext();
new XmlBeanDefinitionReader(context).loadBeanDefinitions("services.xml", "daos.xml");
context.refresh();
```

You can also use the `GroovyBeanDefinitionReader` for Groovy files, as the following example shows:

针对 `Groovy` 文件也可以使用 `GroovyBeanDefinitionReader` ，示例如下：

```java
GenericApplicationContext context = new GenericApplicationContext();
new GroovyBeanDefinitionReader(context).loadBeanDefinitions("services.groovy", "daos.groovy");
context.refresh();
```

You can mix and match such reader delegates on the same `ApplicationContext`, reading bean definitions from diverse configuration sources.

You can then use `getBean` to retrieve instances of your beans. The `ApplicationContext` interface has a few other methods for retrieving beans, but, ideally, your application code should never use them. Indeed, your application code should have no calls to the `getBean()` method at all and thus have no dependency on Spring APIs at all. For example, Spring’s integration with web frameworks provides dependency injection for various web framework components such as controllers and JSF-managed beans, letting you declare a dependency on a specific bean through metadata (such as an autowiring annotation).

你可以在相同的 `ApplicationContext` 上混合匹配 `reader` 委托 ，从不同的配置源读取bean的定义。

然后您可以使用它 `getBean` 来检索 bean 的实例。该`ApplicationContext` 接口有一些其他方法来检索 bean，但理想情况下，您的应用程序代码永远不应该使用它们。实际上，您的应用程序代码根本不应该调用该 `getBean()`方法，因此完全不依赖于 Spring API。例如，Spring 与 Web 框架的集成为各种 Web 框架组件（例如控制器和 JSF 管理的 bean）提供了依赖注入，让您可以通过元数据（例如 `@Autowired` 注解）声明对特定 bean 的依赖。

### 1.3. Bean Overview (Bean概述)

A Spring IoC container manages one or more beans. These beans are created with the configuration metadata that you supply to the container (for example, in the form of XML `<bean/>` definitions).

Within the container itself, these bean definitions are represented as `BeanDefinition` objects, which contain (among other information) the following metadata:

- A package-qualified class name: typically, the actual implementation class of the bean being defined.
- Bean behavioral configuration elements, which state how the bean should behave in the container (scope, lifecycle callbacks, and so forth).
- References to other beans that are needed for the bean to do its work. These references are also called collaborators or dependencies.
- Other configuration settings to set in the newly created object — for example, the size limit of the pool or the number of connections to use in a bean that manages a connection pool.

一个 Spring IoC 容器管理一个或多个 bean。这些 bean 是使用您提供给容器的配置元数据创建的（例如，以 XML`<bean/>`定义的形式 ）。

在容器本身内，这些 bean 定义 表示为`BeanDefinition` 对象，其中包含（除其他信息外）以下元数据：

- 包限定的类名：通常是定义的 bean 的实际实现类。
- Bean 行为配置元素，它说明 Bean 在容器中的行为方式（范围、生命周期回调等）
- 引用 bean 执行其工作所需的其他 bean 。这些引用也称为协作者或依赖项。
- 在新创建的对象中设置的其他配置设置——例如，管理连接池的bean中要使用的连接池的大小限制或可用连接数。

This metadata translates to a set of properties that make up each bean definition. The following table describes these properties:

此元数据转换为组成每个 bean 定义 的一组属性。下表描述了这些属性：

| Property                 | Explained in…                                                |
| :----------------------- | :----------------------------------------------------------- |
| Class                    | [Instantiating Beans](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-class) |
| Name                     | [Naming Beans](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-beanname) |
| Scope                    | [Bean Scopes](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes) |
| Constructor arguments    | [Dependency Injection](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-collaborators) |
| Properties               | [Dependency Injection](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-collaborators) |
| Autowiring mode          | [Autowiring Collaborators](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-autowire) |
| Lazy initialization mode | [Lazy-initialized Beans](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-lazy-init) |
| Initialization method    | [Initialization Callbacks](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-lifecycle-initializingbean) |
| Destruction method       | [Destruction Callbacks](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-lifecycle-disposablebean) |

In addition to bean definitions that contain information on how to create a specific bean, the `ApplicationContext` implementations also permit the registration of existing objects that are created outside the container (by users). This is done by accessing the ApplicationContext’s BeanFactory through the `getBeanFactory()` method, which returns the BeanFactory `DefaultListableBeanFactory` implementation. `DefaultListableBeanFactory` supports this registration through the `registerSingleton(..)` and `registerBeanDefinition(..)` methods. However, typical applications work solely with beans defined through regular bean definition metadata.

除了包含有关如何创建特定 bean 的信息的 bean 定义之外，`ApplicationContext `实现还允许注册在容器外创建的现有对象。这是通过`getBeanFactory()` 方法访问 ApplicationContext 的 BeanFactory 来完成的, 该方法返回BeanFactory 的实现类 `DefaultListableBeanFactory`。`DefaultListableBeanFactory` 通过`registerSingleton(..)`和 `registerBeanDefinition(..)` 方法支持这种注册方式。但是，典型的应用程序仅使用通过常规 bean 定义的元数据定义的 bean。

> Bean metadata and manually supplied singleton instances need to be registered as early as possible, in order for the container to properly reason about them during autowiring and other introspection steps. While overriding existing metadata and existing singleton instances is supported to some degree, the registration of new beans at runtime (concurrently with live access to the factory) is not officially supported and may lead to concurrent access exceptions, inconsistent state in the bean container, or both.
>
> Bean元数据和手动提供的单例实例需要尽早注册，以便容器在自动装配和其他自省步骤中正确地对它们进行推理。虽然在某种程度上支持覆盖现有的元数据和现有的单例实例，但是在运行时注册新bean(与对工厂的实时访问同时进行)并没有得到官方支持，并且可能导致并发访问异常、bean容器中的不一致状态，或者两者都有。

#### 1.3.1. Naming Beans

Every bean has one or more identifiers. These identifiers must be unique within the container that hosts the bean. A bean usually has only one identifier. However, if it requires more than one, the extra ones can be considered aliases.

In XML-based configuration metadata, you use the `id` attribute, the `name` attribute, or both to specify the bean identifiers. The `id` attribute lets you specify exactly one id. Conventionally, these names are alphanumeric ('myBean', 'someService', etc.), but they can contain special characters as well. If you want to introduce other aliases for the bean, you can also specify them in the `name` attribute, separated by a comma (`,`), semicolon (`;`), or white space. As a historical note, in versions prior to Spring 3.1, the `id` attribute was defined as an `xsd:ID` type, which constrained possible characters. As of 3.1, it is defined as an `xsd:string` type. Note that bean `id` uniqueness is still enforced by the container, though no longer by XML parsers.

You are not required to supply a `name` or an `id` for a bean. If you do not supply a `name` or `id` explicitly, the container generates a unique name for that bean. However, if you want to refer to that bean by name, through the use of the `ref` element or a Service Locator style lookup, you must provide a name. Motivations for not supplying a name are related to using [inner beans](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-inner-beans) and [autowiring collaborators](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-autowire).

每个 bean 都有一个或多个标识符。这些标识符在承载 bean 的容器中必须是唯一的。一个 bean 通常只有一个标识符。但是，如果它需要多个，则可以将多余的视为别名。

在基于 XML 的配置元数据中，您可以使用 `id` 属性、`name` 属性或两者一起来指定 bean 标识符。该  `id` 属性允许您指定一个 ID。通常，这些名称是字母数字组成的（'myBean'、'someService' 等），但它们也可以包含特殊字符。如果要为 bean 引入其他别名，也可以在 `name` 属性中指定它们，用逗号 ( `,`)、分号 ( `;`) 或空格分隔。作为历史记录(暂时没有更合适的翻译)，在 Spring 3.1 之前的版本中，`id` 属性被定义为一种 `xsd:ID` 类型，它限制了可能的字符。从 3.1 开始，它被定义为一种 `xsd:string` 类型。请注意，bean `id` 唯一性仍然由容器强约束，但不再由 XML 解析器强约束。

您不需要一定为 bean提供 `name` 或  `id`。如果您没有明确提供，则容器会为该 bean 生成一个唯一的 `name`。但是，如果您想通过 `name` 引用该 bean，通过使用`ref`元素或服务定位器样式查找，则您必须提供名称。不提供名称的动机与使用 [inner beans](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-inner-beans) 和 [autowiring collaborators ](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-autowire)有关.


> Bean Naming Conventions

The convention is to use the standard Java convention for instance field names when naming beans. That is, bean names start with a lowercase letter and are camel-cased from there. Examples of such names include `accountManager`, `accountService`, `userDao`, `loginController`, and so forth.

Naming beans consistently makes your configuration easier to read and understand. Also, if you use Spring AOP, it helps a lot when applying advice to a set of beans related by name.

Bean命名规范

在命名 bean 时对实例字段名称使用标准 Java 规范。也就是说，bean 名称以小写字母开头，然后开始使用驼峰式大小写。此类名称的示例包括`accountManager`、 `accountService`、`userDao`、`loginController`等等。

保持 bean命名的一致性可以使您的配置更易于阅读和理解。此外，如果您使用 Spring AOP，在应用于一组按名称相关的 bean 时使用这条建议会很有帮助。

> With component scanning in the classpath, Spring generates bean names for unnamed components, following the rules described earlier: essentially, taking the simple class name and turning its initial character to lower-case. However, in the (unusual) special case when there is more than one character and both the first and second characters are upper case, the original casing gets preserved. These are the same rules as defined by `java.beans.Introspector.decapitalize` (which Spring uses here).
>
> 通过在类路径中扫描组件，Spring为未命名组件生成bean名称，遵循前面描述的规则: 本质上，采用简单的类名并将其初始字符转换为小写。但是，在(不常见的)特殊情况下，当有多个字符且第一个和第二个字符都是大写时，保留原来的大小写。这些规则与 `java.beans.Introspector.decapitalize` 定义的规则相同(Spring在这里使用的)。

##### Aliasing a Bean outside the Bean Definition (在Bean定义之外给Bean起别名)

In a bean definition itself, you can supply more than one name for the bean, by using a combination of up to one name specified by the `id` attribute and any number of other names in the `name` attribute. These names can be equivalent aliases to the same bean and are useful for some situations, such as letting each component in an application refer to a common dependency by using a bean name that is specific to that component itself.

Specifying all aliases where the bean is actually defined is not always adequate, however. It is sometimes desirable to introduce an alias for a bean that is defined elsewhere. This is commonly the case in large systems where configuration is split amongst each subsystem, with each subsystem having its own set of object definitions. In XML-based configuration metadata, you can use the `<alias/>` element to accomplish this. The following example shows how to do so:

在bean定义本身中，通过使用id属性指定的最多一个名称和name属性中任意数量的其他名称的组合，您可以为bean提供多个名称。这些名称可以是同一个bean的等效别名，这在某些情况下非常有用，例如通过使用特定于该组件本身的bean名称，让应用程序中的每个组件引用公共依赖项。

但是，指定bean实际定义的所有别名并不总是足够的。有时需要为在其他地方定义的bean引入别名。在大型系统中很常见的一个例子是，配置拆分在每个子系统之间，每个子系统都有自己的对象定义集。在基于xml的配置元数据中，可以使用<alias/>元素来实现这一点。下面的例子展示了如何做到这一点:
In this case, a bean (in the same container) named `fromName` may also, after the use of this alias definition, be referred to as `toName`.

For example, the configuration metadata for subsystem A may refer to a DataSource by the name of `subsystemA-dataSource`. The configuration metadata for subsystem B may refer to a DataSource by the name of `subsystemB-dataSource`. When composing the main application that uses both these subsystems, the main application refers to the DataSource by the name of `myApp-dataSource`. To have all three names refer to the same object, you can add the following alias definitions to the configuration metadata:

```xml
<alias name="myApp-dataSource" alias="subsystemA-dataSource"/>
<alias name="myApp-dataSource" alias="subsystemB-dataSource"/>
```

在这种情况下, 名称为`fromName`的bean（在同一容器中）, 在使用此别名定义后也可以称为`toName`.

例如，子系统A的配置元数据可以通过 `subsystemA-dataSource` 的名称引用一个数据源。子系统B的配置元数据可以通过 `subsystemB-dataSource` 的名称来引用一个数据源。当组合使用这两个子系统的主应用程序时，主应用程序通过 `myApp-dataSource` 的名称引用数据源。要让这三个名称都引用同一个对象，可以在配置元数据中添加以下别名定义:

Now each component and the main application can refer to the dataSource through a name that is unique and guaranteed not to clash with any other definition (effectively creating a namespace), yet they refer to the same bean.

现在，每个组件和主应用程序都可以通过一个惟一的名称来引用dataSource，这个名称保证不会与任何其他定义冲突(有效地创建了一个名称空间)，但它们引用的是同一个bean。

> Java-configuration
>
> If you use Javaconfiguration, the `@Bean` annotation can be used to provide aliases. See [Using the `@Bean` Annotation](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-java-bean-annotation) for details.

#### 1.3.2. Instantiating Beans

A bean definition is essentially a recipe for creating one or more objects. The container looks at the recipe for a named bean when asked and uses the configuration metadata encapsulated by that bean definition to create (or acquire) an actual object.

If you use XML-based configuration metadata, you specify the type (or class) of object that is to be instantiated in the `class` attribute of the `<bean/>` element. This `class` attribute (which, internally, is a `Class` property on a `BeanDefinition` instance) is usually mandatory. (For exceptions, see [Instantiation by Using an Instance Factory Method](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-class-instance-factory-method) and [Bean Definition Inheritance](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-child-bean-definitions).) You can use the `Class` property in one of two ways:

- Typically, to specify the bean class to be constructed in the case where the container itself directly creates the bean by calling its constructor reflectively, somewhat equivalent to Java code with the `new` operator.
- To specify the actual class containing the `static` factory method that is invoked to create the object, in the less common case where the container invokes a `static` factory method on a class to create the bean. The object type returned from the invocation of the `static` factory method may be the same class or another class entirely.

一个bean定义本质上是创建一个或多个对象的方法。容器在被询问时查看命名bean的配方(暂时没有合适的翻译)，并使用该bean定义 封装的配置元数据创建(或获取)实际对象。

如果使用基于 XML 的配置元数据，则你需要指定要在 `<bean/>`元素的class属性中实例化的对象的类型(或类), 这个 `class`属性（在内部，它是一个`BeanDefinition` 实例的`Class`属性）通常是强制性的。（对于例外情况，请参阅 [使用实例工厂方法](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-class-instance-factory-method)和[Bean 定义继承](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-child-bean-definitions)[进行实例化](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-class-instance-factory-method)。）你可以用以下两种方式之一来使用 `Class` 属性:

- 通常，在容器本身通过反射调用构造函数直接创建bean的情况下，指定要构造的bean类，这在某种程度上相当于带有new操作符的Java代码。
- 指定包含创建对象的静态工厂方法的实际类, 容器调用类上的静态工厂方法来创建bean的情况不太常见, 调用静态工厂方法返回的对象类型可以是同一个类，也可以是另一个完全相同的类。

> Nested class names
>
> If you want to configure a bean definition for a nested class, you may use either the binary name or the source name of the nested class.
>
> For example, if you have a class called `SomeThing` in the `com.example` package, and this `SomeThing` class has a `static` nested class called `OtherThing`, they can be separated by a dollar sign (`$`) or a dot (`.`). So the value of the `class` attribute in a bean definition would be `com.example.SomeThing$OtherThing` or `com.example.SomeThing.OtherThing`.
>
> 嵌套类名
>
> 如果您想为嵌套类配置bean定义，您可以使用嵌套类的二进制名称或源名称。
>
> 例如，如果你在com.example包中有一个叫做SomeThing的类。这个SomeThing类有一个名为OtherThing的静态嵌套类，它们可以用美元符号($)或点(.)分隔。因此，bean定义中的class属性的值应该是`com.example.SomeThing$OtherThing` 或者 `com.example.SomeThing.OtherThing`.

##### Instantiation with a Constructor (使用构造函数进行实例化)

When you create a bean by the constructor approach, all normal classes are usable by and compatible with Spring. That is, the class being developed does not need to implement any specific interfaces or to be coded in a specific fashion. Simply specifying the bean class should suffice. However, depending on what type of IoC you use for that specific bean, you may need a default (empty) constructor.

The Spring IoC container can manage virtually any class you want it to manage. It is not limited to managing true JavaBeans. Most Spring users prefer actual JavaBeans with only a default (no-argument) constructor and appropriate setters and getters modeled after the properties in the container. You can also have more exotic non-bean-style classes in your container. If, for example, you need to use a legacy connection pool that absolutely does not adhere to the JavaBean specification, Spring can manage it as well.

With XML-based configuration metadata you can specify your bean class as follows:

```xml
<bean id="exampleBean" class="examples.ExampleBean"/>

<bean name="anotherExample" class="examples.ExampleBeanTwo"/>
```

当您通过构造函数方法创建一个bean时，所有普通的类都可以被Spring使用，并且与Spring兼容。也就是说，正在开发的类不需要实现任何特定的接口，也不需要以特定的方式编码。只需指定bean类就足够了。但是，根据您为特定bean使用的IoC类型，您可能需要一个默认(空)构造函数。

Spring IoC容器实际上可以管理您希望它管理的任何类。它并不局限于管理真正的Javabean。大多数Spring用户更喜欢只有默认(无参数)构造函数和根据容器中的属性适当添加的setter和getter方法的实际Javabean。您还可以在容器中添加更多非bean风格的类。例如，如果您需要使用绝对不遵守JavaBean规范的遗留连接池，Spring也可以管理它。

使用基于xml的配置元数据，你可以像下边这样指定你的bean类:

```xml
<bean id="exampleBean" class="examples.ExampleBean"/>

<bean name="anotherExample" class="examples.ExampleBeanTwo"/>
```

有关向构造函数提供参数（如果需要）和在构造对象后设置对象实例属性的机制的详细信息，请参阅[Injecting Dependencies](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-collaborators).


##### Instantiation by Using an Instance Factory Method (使用实例工厂方法进行实例化)

Similar to instantiation through a [static factory method](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-class-static-factory-method), instantiation with an instance factory method invokes a non-static method of an existing bean from the container to create a new bean. To use this mechanism, leave the `class` attribute empty and, in the `factory-bean` attribute, specify the name of a bean in the current (or parent or ancestor) container that contains the instance method that is to be invoked to create the object. Set the name of the factory method itself with the `factory-method` attribute. The following example shows how to configure such a bean:

与通过[静态工厂方法](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-class-static-factory-method)实例化类似，使用实例[工厂方法](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-class-static-factory-method)实例化从容器中调用现有 bean 的非静态方法来创建新 bean。要使用此机制，请将`class`属性留空，并在`factory-bean`属性中指定当前（或父或祖先）容器中 bean 的名称，该容器包含要调用来创建对象的实例方法。使用`factory-method`属性设置工厂方法本身的名称。以下示例显示了如何配置此类 bean：

```xml
<!-- the factory bean, which contains a method called createInstance() -->
<bean id="serviceLocator" class="examples.DefaultServiceLocator">
    <!-- inject any dependencies required by this locator bean -->
</bean>

<!-- the bean to be created via the factory bean -->
<bean id="clientService"
    factory-bean="serviceLocator"
    factory-method="createClientServiceInstance"/>
```

The following example shows the corresponding class:

以下示例显示了相应的类：

```java
public class DefaultServiceLocator {

    private static ClientService clientService = new ClientServiceImpl();

    public ClientService createClientServiceInstance() {
        return clientService;
    }
}
```

One factory class can also hold more than one factory method, as the following example shows:

一个工厂类也可以包含多个工厂方法，如下例所示：

```xml
<bean id="serviceLocator" class="examples.DefaultServiceLocator">
    <!-- inject any dependencies required by this locator bean -->
</bean>

<bean id="clientService"
    factory-bean="serviceLocator"
    factory-method="createClientServiceInstance"/>

<bean id="accountService"
    factory-bean="serviceLocator"
    factory-method="createAccountServiceInstance"/>
```

The following example shows the corresponding class:

以下示例显示了相应的类：

```java
public class DefaultServiceLocator {

    private static ClientService clientService = new ClientServiceImpl();

    private static AccountService accountService = new AccountServiceImpl();

    public ClientService createClientServiceInstance() {
        return clientService;
    }

    public AccountService createAccountServiceInstance() {
        return accountService;
    }
}
```

This approach shows that the factory bean itself can be managed and configured through dependency injection (DI). See [Dependencies and Configuration in Detail](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-properties-detailed).

这种方法表明工厂 bean 本身可以通过依赖注入 (DI) 进行管理和配置。请参阅详细的[依赖关系和配置](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-properties-detailed)。

>  In Spring documentation, "factory bean" refers to a bean that is configured in the Spring container and that creates objects through an [instance](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-class-instance-factory-method) or [static](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-class-static-factory-method) factory method. By contrast, `FactoryBean` (notice the capitalization) refers to a Spring-specific [`FactoryBean`](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-extension-factorybean) implementation class.
>
>  在 Spring 文档中，“factory bean”是指在 Spring 容器中配置并通过[实例](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-class-instance-factory-method)或 [静态](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-class-static-factory-method)工厂方法创建对象的 bean 。相比之下， `FactoryBean`（注意大写）是指特定于 Spring 的 [`FactoryBean`](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-extension-factorybean)实现类。
