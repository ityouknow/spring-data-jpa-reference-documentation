例47. 声明一个Jackson库populator
```XML
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:repository="http://www.springframework.org/schema/data/repository"
xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans.xsd
http://www.springframework.org/schema/data/repository
http://www.springframework.org/schema/data/repository/spring-repository.xsd">

<repository:jackson2-populator locations="classpath:data.json" />
</beans>
```
这个声明会让Jackson对象封装器读取并反序列化data.json文件。

通过检查JSON文档的`_class`属性来确定解组后的JSON对象的类型。框架最终会选择合适的存储库处理被反序列化后的对象。

为了使用XML来定义存储库应被填充的数据，您可以使用`unmarshaller-populator`元素。您可以使用Sprong OXM提供的XML编组器来配置。具体细节请参考[Spring reference document](https://docs.spring.io/spring/docs/5.0.2.RELEASE/spring-framework-reference/data-access.html#oxm)。

例48. 声明一个解组存储库populator(使用JAXB)
```XML
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:repository="http://www.springframework.org/schema/data/repository"
xmlns:oxm="http://www.springframework.org/schema/oxm"
xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans.xsd
http://www.springframework.org/schema/data/repository
http://www.springframework.org/schema/data/repository/spring-repository.xsd
http://www.springframework.org/schema/oxm
http://www.springframework.org/schema/oxm/spring-oxm.xsd">

<repository:unmarshaller-populator locations="classpath:data.json"
unmarshaller-ref="unmarshaller" />

<oxm:jaxb2-marshaller contextPath="com.acme" />

</beans>
```

#### 3.8.4. 遗留的Web支持

Spring MVC的域类Web绑定

倘若您正在开发Spring MVC Web应用程序，您通常必须从URL中解析域类ID。默认情况下，您的任务是将请求参数或URL部分转换为域类，以将其交给下面的层，或直接在实体上执行业务逻辑。如下所示：

```Java
@Controller
@RequestMapping("/users")
class UserController {

private final UserRepository userRepository;

UserController(UserRepository userRepository) {
Assert.notNull(repository, "Repository must not be null!");
this.userRepository = userRepository;
}

@RequestMapping("/{id}")
String showUserForm(@PathVariable("id") Long id, Model model) {

// Do null check for id
User user = userRepository.findById(id);
// Do null check for user

model.addAttribute("user", user);
return "user";
}
}
```

首先为每个控制器声明一个存储库依赖关系，分别查找由控制器或存储库管理的实体。查找实体总是通过findById(...)的调用模板来实现。幸运的是，Spring提供了注册自定义组件的方法，允许将String值转换为任意类型。

PropertyEditors
在Spring的3.0版本之前，必须使用简单的Java PropertyEditors。为了集成它，Spring Data提供了一个`DomainClassPropertyEditorRegistrar`。它将会在`ApplicationContext`中查找所有注册过的Spring Data存储库，并为所管理的域类注册一个自定义的`PropertyEditor`。

```XML
<bean class="….web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter">
<property name="webBindingInitializer">
<bean class="….web.bind.support.ConfigurableWebBindingInitializer">
<property name="propertyEditorRegistrars">
<bean class="org.springframework.data.repository.support.DomainClassPropertyEditorRegistrar" />
</property>
</bean>
</property>
</bean>
```
如果您在之前的Spring MVC例子中配置过，您可以通过如下方式配置控制器，这将消除大量冗余的代码。

```Java
@Controller
@RequestMapping("/users")
class UserController {

@RequestMapping("/{id}")
String showUserForm(@PathVariable("id") User user, Model model) {

model.addAttribute("user", user);
return "userForm";
}
}
```
## 第四章 JPA存储库

本章将指明JPA对存储库的支持。这建立在第三章中所阐明的核心存储库的支持基础之上。因此，确保您充分理解第三章中的基本概念。

#
## 4.1. 介绍

#### 4.1.1. Spring名称空间

Spring Data的JPA模块包含一个允许定义存储库bean的自定义名称空间。 它还包含JPA特有的某些功能和元素属性。通常可以使用`repositories`元素来设置JPA存储库：

例49. 通过名称空间设置JPA存储库
```XML
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:jpa="http://www.springframework.org/schema/data/jpa"
xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans.xsd
http://www.springframework.org/schema/data/jpa
http://www.springframework.org/schema/data/jpa/spring-jpa.xsd">

<jpa:repositories base-package="com.acme.repositories" />

</beans>
```
在[创建repository实例](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repositories.create-instances)中已经详细描述了如何使用这个元素查询Spring Data存储库。除此之外，它激活了所有使用`@Repository`注释的bean的持久性异常转换，以使JPA持久性提供者抛出的异常转换为Spring的DataAccessException层次结构。

自定义名称空间属性

除了`repositories`元素的默认属性之外，JPA名称空间还提供了额外的属性来获得对存储库设置的更精细的控制：

表2. 自定义特定于JPA的存储库元素的属性

|entity-manager-factory-ref|显式地连接`EntityManagerFactory`，和由`repositories`元素检测出的存储库一起使用。通常在应用程序中使用多个`EntityManagerFactory`bean时使用。如果没有配置，我们将在`ApplicationContext`中查找名为`entityManagerFactory`的`EntityManagerFactory`bean|
|:----:|:----:|
|transaction-manager-ref|显式地连接`PlatformTransactionManager`,和由`repositories`元素检测出的存储库一起使用。通常只有在配置了多个事务管理器和/或`EntityManagerFactory`bean时才需要。默认为当前`ApplicationContext`中的一个定义的`PlatformTransactionManager`|

请注意，如果没有定义显式的`transaction-manager-ref`，我们需要呈现一个名为`transactionManager`的`PlatformTransactionManager`bean。

#### 4.1.2. 基于注解的配置

Spring Data JPA存储库支持既可以通过XML命名空间激活，还可以通过JavaConfig使用注解。
