## 第五章 JPA Repositories

本章将指出支持JPA的repository接口的所有特性。这建立在 [Working with Spring Data Repositories](http://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repositories)中解释过的核心的repository支持。所以要确保你对那里所解释的基本概念有了正确的理解。

###5.1 介绍

####5.1.1 Spring命名空间

Spring Data的JPA模块包含允许定义仓库实体的自定义命名空间。它也包含专属于JPA的确切特征和元素属性。一般来说，JPA仓库可以使用```repositories```元素来设置：

*Example 42. 使用命名空间设置JPA仓库*

```
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

如在[创建存储库实例]()中所描述的那样，使用此元素查找Spring Data repositories。除此之外，它使所有被```@Repository```注解的bean的持久化异常转换生效，从而使JPA持久提供者所抛出的异常转化成Spring的DataAccessException层级。

#####Custom namespace attributes

除了```repositories```元素的默认属性，JPA命名空间提供额外的属性来获得repositories建立时的更多细节控制

*表2 自定义属性jpa特异性repositories element of the*

| entity-manager-factory-ref | Explicitly wire the `EntityManagerFactory` to be used with the repositories being detected by the `repositories` element. Usually used if multiple `EntityManagerFactory` beans are used within the application. If not configured we will automatically lookup the `EntityManagerFactory` bean with the name `entityManagerFactory` in the `ApplicationContext`. |
| :------------------------- | ---------------------------------------- |
| transaction-manager-ref    | Explicitly wire the `PlatformTransactionManager` to be used with the repositories being detected by the `repositories` element. Usually only necessary if multiple transaction managers and/or `EntityManagerFactory` beans have been configured. Default to a single defined `PlatformTransactionManager` inside the current `ApplicationContext`. |

请注意，如果没有明确的```transaction-manager-ref```被定义，我们需要一个名为transactionManager的PlatformTransactionManager bean。

#### 5.1.2基于注解的配置

Spring Data JPA的repository不仅可以通过XML命名空间的方式生效，而且可以使用JavaConfig注解的形式。

*Example 43. Spring Data JPA repositories using JavaConfig*

```java
@Configuration
@EnableJpaRepositories
@EnableTransactionManagement
class ApplicationConfig {

  @Bean
  public DataSource dataSource() {

    EmbeddedDatabaseBuilder builder = new EmbeddedDatabaseBuilder();
    return builder.setType(EmbeddedDatabaseType.HSQL).build();
  }

  @Bean
  public LocalContainerEntityManagerFactoryBean entityManagerFactory() {

    HibernateJpaVendorAdapter vendorAdapter = new HibernateJpaVendorAdapter();
    vendorAdapter.setGenerateDdl(true);

    LocalContainerEntityManagerFactoryBean factory = new LocalContainerEntityManagerFactoryBean();
    factory.setJpaVendorAdapter(vendorAdapter);
    factory.setPackagesToScan("com.acme.domain");
    factory.setDataSource(dataSource());
    return factory;
  }

  @Bean
  public PlatformTransactionManager transactionManager() {

    JpaTransactionManager txManager = new JpaTransactionManager();
    txManager.setEntityManagerFactory(entityManagerFactory());
    return txManager;
  }
}
```

上面展现的配置类使用spring-jdbc的API```EmbeddedDatabaseBuilder```创建了一个嵌入式的HSQL数据库。接下来我们创建一个```EntityManagerFactory```并且使用Hibernate作为持久化提供者的例子。最后声明的基础组件是```JpaTransactionManager```。最后我们使用与XML命名空间一样相同属性的```@EnableJpaRepositories```注解来使Spring Data JPA生效。如果基础包没有被配置，它将使用配置类所在的包。

###5.2 持久化实体

#### 5.2.1 保存实体

### 5.3 Query方法

####5.3.1. Query查询策略

JPA模块支持手动定义查询语句

#####声明查询

#### 5.3.2. 声明Query

#### 5.3.3. 使用JPA预定义查询

#### 5.3.4. 使用@Query

#### 5.3.5. 使用Sort

#### 5.3.6. 使用命名参数

#### 5.3.7. 使用SpEL表达式

#### 5.3.8. Modifying queries

#### 5.3.9. Applying query hints

#### 5.3.10. Configuring Fetch- and LoadGraphs

#### 5.3.11. Projections

### 5.4. Stored procedures

### 5.5. Specifications











