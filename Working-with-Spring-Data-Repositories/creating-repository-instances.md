### 3.5. 创建repository实例

在这个部分，你创建实例和为repository接口定义的bean。这样做的一个方法是使用Spring的名称空间，这是与每个Spring Data模块，支持存储机制，虽然我们一般建议使用JAVA配置风格的配置。 

#### 3.5.1. XML配置

每一个Spring Data模块都包含repositories元素能够让你简单的基于base-package定义来进行Spring扫描。

例 21. 通过XML来开启Spring Data repositories
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans:beans xmlns:beans="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns="http://www.springframework.org/schema/data/jpa"
  xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/data/jpa
    http://www.springframework.org/schema/data/jpa/spring-jpa.xsd">

  <repositories base-package="com.acme.repositories" />

</beans:beans>
```
在上面这个例子中，Spring会在`com.acme.repositories`和它的子包中扫描继承`Repository`或其子类的接口。框架会为这些找到的接口注册持久化`FactoryBean`（技术特定），为处理查询方法的调用创建合适的代理。每个bean注册的名称都是来自于接口名，所以一个名为`UserRepository`的接口注册名称是`userRepository`。`base-package`属性允许使用通配符，所以你可以定义一个被扫描包名的模式。

**使用Filter**

默认情况下，框架会为在back-package定义的包中每个继承`Repository`或其子接口的接口创建一个bean实例。然而，你可能想要更精细地控制为哪个接口创建bean实例。为此，你可以在`<repositories />`内使用`<include-filter />`和`<exclude-filter >`。语义和Spring的上下文命名空间中的元素是完全等价的。详细信息请参阅[Spring参考文档](https://docs.spring.io/spring/docs/5.0.2.RELEASE/spring-framework-reference/core.html#beans-scanning-filters)对这些元素的介绍。

例如，要将某些接口从作为Repository的实例中排除，可以使用如下配置：

例 22. 使用exclude-filter元素
```xml
<repositories base-package="com.acme.repositories">
  <context:exclude-filter type="regex" expression=".*SomeRepository" />
</repositories>
```

这个例子排除了以SomeRepository结尾的接口被实例化。

#### 3.5.2. Java配置

也可以在一个Java配置的类使用`@Enable${store}Repositories`注解来触发repository框架。有关Spring容器的基于Java配置的介绍，请参阅参考文档[1](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#_footnote_1)。

以下是一个启用Spring Data repositories的配置示例。

例 23. 基于repository配置的注解示例

```java
@Configuration
@EnableJpaRepositories("com.acme.repositories")
class ApplicationConfiguration {

  @Bean
  EntityManagerFactory entityManagerFactory() {
    // …
  }
}
```

> 该示例使用特定于JPA的注解，您可以根据实际使用的store模块做相应改变。这同样适用于`EntityManagerFactory`bean的定义。请参阅有关store特定配置的章节。

#### 3.5.3. 独立使用

您还可以使用Spring容器之外的repository基础架构。










