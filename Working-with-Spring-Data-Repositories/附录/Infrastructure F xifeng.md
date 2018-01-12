## 基础配置
1.目前我已经实现了基于HibernateDaoSupport的存储库层。 我使用Spring的AnnotationSessionFactoryBean创建了
一个SessionFactory。 如何让Spring Data存储库在这个环境中工作？

您必须使用HibernateJpaSessionFactoryBean替换AnnotationSessionFactoryBean，如下所示：

##### 例如110.从HibernateEntityManagerFactory查找SessionFactory
~~~java
<bean id="sessionFactory" class="org.springframework.orm.jpa.vendor.HibernateJpaSessionFactoryBean">
  <property name="entityManagerFactory" ref="entityManagerFactory"/>
</bean>
~~~
## Auditing
我想使用Spring Data JPA Auditing功能，但是我的数据库已经设置好，可以在实体上设置修改和创建日期。 如何防止
Spring Data以编程方式设置日期。

只需使用auditing命名空间元素的set-dates属性为false即可。

# 附录 F：网络连接失败
##### **AOP**
    Aspect oriented programming

##### **Commons DBCP**
    Commons DataBase Connection Pools - Library of the Apache foundation offering pooling implementations of the DataSource interface.

##### **CRUD**
    Create, Read, Update, Delete - Basic persistence operations

##### **DAO**
    Data Access Object - Pattern to separate persisting logic from the object to be persisted

##### **Dependency Injection**
    Pattern to hand a component’s dependency to the component from outside, freeing the component to lookup the dependant itself. For more information see http://en.wikipedia.org/wiki/Dependency_Injection.

##### **EclipseLink**
    Object relational mapper implementing JPA - http://www.eclipselink.org

##### **Hibernate**
    Object relational mapper implementing JPA - http://www.hibernate.org

##### **JPA**
    Java Persistence API

##### **Spring**
    Java application framework - http://projects.spring.io/spring-framework
_______________________________________________________________________________________________________
1. JavaConfig in the Spring reference documentation
2. Spring HATEOAS - https://github.com/SpringSource/spring-hateoas
3. see XML configuration
4. see XML configuration