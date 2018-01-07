#
4.1.2. 基于注释的配置

Spring Data JPA存储库支持不仅可以通过XML命名空间激活，还可以通过JavaConfig使用注释。

##### _例50.使用JavaConfig的Spring Data JPA存储库_

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

> 重要的是要创造`LocalContainerEntityManagerFactoryBean`不和`EntityManagerFactory`直接，因为前者还参加了异常转换机制，除了简单地创建`EntityManagerFactory`。

刚刚显示的配置类使用`EmbeddedDatabaseBuilderspring`-jdbc 的API 设置嵌入式HSQL数据库。然后我们设置一个`EntityManagerFactory`并使用Hibernate作为示例持久化提供者。这里声明的最后一个基础设施组件是`JpaTransactionManager`。我们最终使用`@EnableJpaRepositories`基本上带有与XML名称空间相同的属性的注释来激活Spring Data JPA存储库。如果没有配置基础软件包，它将使用配置类所在的软件包。

# 4.2. 持久化实体

#### 4.2.1. 保存实体 {#jpa.entity-persistence.saving-entites}

保存一个实体可以通过CrudRepository.save\(…\)-Method 来执行。它将持久话或合并给定的实体使用基础的JPA EntityManager。如果实体没有被保存，Spring Data JPA将通过调用entityManager.persist\(…\)方法保存实体，否则该entityManager.merge\(…\)方法将被调用。

##### 实体状态检测策略 {#_entity_state_detection_strategies}

Spring Data JPA提供了以下策略来检测一个实体是否是新的：

_表3.用于检测实体在Spring Data JPA中是否新增的选项_

| ID属性检查\(默认\) | 默认情况下，Spring Data JPA检查给定实体的标识符属性。如果标识符属性是null，则该实体将被认为是新的，否则不是新的。 |
| :--- | :--- |
| 实现Persistable | 如果一个实体实现Persistable，Spring Data JPA将把新的检测委托给isNew\(…\)实体的方法。有关详细信息，请参阅[JavaDoc。](https://docs.spring.io/spring-data/data-commons/docs/current/api/index.html?org/springframework/data/domain/Persistable.html) |
| 实现EntityInformation | 您可以通过创建子类并相应地重写该方法来自定义实现中`EntityInformation`使用的抽象。然后您必须将自定义的实现注册为一个Spring bean。请注意，这应该是很少有必要的。有关详细信息，请参阅[JavaDoc](https://docs.spring.io/spring-data/data-jpa/docs/current/api/index.html?org/springframework/data/jpa/repository/support/JpaRepositoryFactory.html)。`SimpleJpaRepositoryJpaRepositoryFactorygetEntityInformation(…)JpaRepositoryFactory` |

### 4.3. 查询方法 {#jpa.query-methods}

#### 4.3.1.查询轮询策略 {#jpa.sample-app.finders.strategies}

JPA模块支持以String形式手动定义查询，或者从方法名称派生。

##### 声明的查询 {#_declared_queries}

尽管从方法名获得查询是非常方便的，但是可能会遇到这样的情况：方法名解析器不支持要使用的关键字，或者方法名称会变得不必要的难看。所以，你可以通过命名约定使用JPA命名查询（请参阅[使用JPA NamedQueries](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.query-methods.named-queries)获取更多信息）或相当具有注解你的查询方法`@Query`（请参阅[使用@Query](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.query-methods.at-query)了解详细信息）。

#### 4.3.2.查询创建 {#jpa.query-methods.query-creation}

通常，JPA的查询创建机制按[查询方法中的](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repositories.query-methods)描述工作。以下是JPA查询方法转化为的一个简短示例:

##### _例51.从方法名称创建查询_

```java
public interface UserRepository extends Repository<User, Long> {

List<User> findByEmailAddressAndLastname(String emailAddress, String lastname);
}
```

我们将使用从这个JPA标准API查询但本质上这转换为以下查询：select u from User u where u.emailAddress = ?1 and u.lastname = ?2。Spring Data JPA将执行属性检查并遍历[属性表达式](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repositories.query-methods.query-property-expressions)中所述的嵌套属性。下面是对JPA支持的关键字的概述，以及包含该关键字本质的翻译方法。

_表4.方法名称中支持的关键字_

| **关键词** | **示例** | **JPQL片段** |
| :--- | :--- | :--- |
| And | findByLastnameAndFirstname | … where x.lastname = ?1 and x.firstname = ?2 |
| Or | findByLastnameOrFirstname | … where x.lastname = ?1 or x.firstname = ?2 |
| Is,Equals | findByFirstname，findByFirstnameIs，findByFirstnameEquals | … where x.firstname = ?1 |
| Between | findByStartDateBetween | … where x.startDate between ?1 and ?2 |
| LessThan | findByAgeLessThan | … where x.age &lt; ?1 |
| LessThanEqual | findByAgeLessThanEqual | … where x.age &lt;= ?1 |
| GreaterThan | findByAgeGreaterThan | … where x.age &gt; ?1 |
| GreaterThanEqual | findByAgeGreaterThanEqual | … where x.age &gt;= ?1 |
| After | findByStartDateAfter | … where x.startDate &gt; ?1 |
| Before | findByStartDateBefore | … where x.startDate &lt; ?1 |
| IsNull | findByAgeIsNull | … where x.age is null |
| IsNotNull,NotNull | findByAge\(Is\)NotNull | … where x.age not null |
| Like | findByFirstnameLike | … where x.firstname like ?1 |
| NotLike | findByFirstnameNotLike | … where x.firstname not like ?1 |
| StartingWith | findByFirstnameStartingWith | … where x.firstname like ?1（参数绑定附加%） |
| EndingWith | findByFirstnameEndingWith | … where x.firstname like ?1（参数与预先绑定%） |
| Containing | findByFirstnameContaining | … where x.firstname like ?1（参数绑定%） |
| OrderBy | findByAgeOrderByLastnameDesc | … where x.age = ?1 order by x.lastname desc |
| Not | findByLastnameNot | … where x.lastname &lt;&gt; ?1 |
| In | findByAgeIn\(Collection&lt;Age&gt; ages\) | … where x.age in ?1 |
| NotIn | findByAgeNotIn\(Collection&lt;Age&gt; ages\) | … where x.age not in ?1 |
| True | findByActiveTrue\(\) | … where x.active = true |
| False | findByActiveFalse\(\) | … where x.active = false |
| IgnoreCase | findByFirstnameIgnoreCase | … where UPPER\(x.firstame\) = UPPER\(?1\) |

> `In`并且`NotIn`还可以接受任何`Collection`参数的子类以及数组或可变参数。对于逻辑运算符相同的其他语法版本，请检查存储库查询关键字。

#### 4.3.3.使用JPA NamedQueries {#jpa.query-methods.named-queries}

> 这些示例使用简单的&lt;named-query /&gt;元素和@NamedQuery注释。这些配置元素的查询必须在JPA查询语言中定义。当然，你可以使用&lt;named-native-query /&gt;或@NamedNativeQuery过。这些元素允许您通过失去数据库平台独立性来在本地SQL中定义查询。

### XML命名查询定义

要使用XML配置，只需将必要的`<named-query />`元素添加到`orm.xml`位于`META-INF`classpath文件夹中的JPA配置文件中。命名查询的自动调用是通过使用一些定义的命名约定来启用的。有关更多详情，请参阅下文

##### _例52. XML命名查询配置_

```java
<named-query name="User.findByLastname">
<query>select u from User u where u.lastname = ?1</query>
</named-query>
```

正如你所看到的，查询有一个特殊的名字，将在运行时用来解析它。

##### 注释配置 {#_annotation_configuration}

注释配置的好处是不需要另外的配置文件进行编辑，可能会降低维护成本。您需要为每个新的查询声明重新编译您的域类，从而为此付出代价。

_**例53.基于注释的命名查询配置**_

```java
@Entity
@NamedQuery(name = "User.findByEmailAddress",
query = "select u from User u where u.emailAddress = ?1")
public class User {

}
```


