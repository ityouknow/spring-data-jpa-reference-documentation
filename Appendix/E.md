## 附录 E: 频繁被问起的问题

**常见的**

1. *我想要得到更详细的日志信息，那么在JpaRepository中哪个方法将被调用，我该如何获得呢？*

你可以使用Spring提供的`CustomizableTraceInterceptor`：

```xml
<bean id="customizableTraceInterceptor" class="
  org.springframework.aop.interceptor.CustomizableTraceInterceptor">
  <property name="enterMessage" value="Entering $[methodName]($[arguments])"/>
  <property name="exitMessage" value="Leaving $[methodName](): $[returnValue]"/>
</bean>

<aop:config>
  <aop:advisor advice-ref="customizableTraceInterceptor"
    pointcut="execution(public * org.springframework.data.jpa.repository.JpaRepository+.*(..))"/>
</aop:config>
```

**基础配置**

1. *目前我已经实现了基于HibernateDaoSupport的存储库层。 我使用Spring的AnnotationSessionFactoryBean创建了
一个SessionFactory。 如何让Spring Data存储库在这个环境中工作？*

您必须使用HibernateJpaSessionFactoryBean替换AnnotationSessionFactoryBean，如下所示：

*例 110. 从HibernateEntityManagerFactory查找SessionFactory*
```java
<bean id="sessionFactory" class="org.springframework.orm.jpa.vendor.HibernateJpaSessionFactoryBean">
  <property name="entityManagerFactory" ref="entityManagerFactory"/>
</bean>
```
**Auditing**

1. *我想使用Spring Data JPA Auditing功能，但是我的数据库已经设置好，可以在实体上设置修改和创建日期。 如何防止
Spring Data以编程方式设置日期。*

只需使用auditing命名空间元素的set-dates属性为false即可。