### 3.3. 定义repository的接口

首先需要定义实体类的接口，接口必须继承repository并且输入实体类型和ID类型，如果需要用到CRUD方法，可以使用`CrudRepository`来替代`Repository`。

#### 3.3.1. 自定义接口

通常,您的存储库接口将会扩展`Repository`, `CrudRepository`或`PagingAndSortingRepository`。 另外,如果你不想继承Spring Data接口,还可以注释库接口`@RepositoryDefinition`。 扩展`CrudRepository`公开了一套完整的方法来操作您的实体。 如果你喜欢选择调用方法,简单地从`CrudRepository`中复制你想要的方法到你的repository。

> 这允许您在已有的Spring Data存储库功能的基础上弹性地定义自己的抽象。

例7.有选择地公开CRUD方法

```java
@NoRepositoryBean
interface MyBaseRepository<T, ID extends Serializable> extends Repository<T, ID> {

  T findOne(ID id);

  T save(T entity);
}

interface UserRepository extends MyBaseRepository<User, Long> {
  User findByEmailAddress(EmailAddress emailAddress);
}
```

第一步你定义了一个公共基础的接口提供了`findOne(…)`和`save(...)`方法,这些方法将会引入到你选择的spring Data的实现类中，例如JPA：`SimpleJpaRepository`,因为他们匹配`CrudRepository`的方法签名，所以`UserRepository`将会具备 save Users的功能和`findOne(…)`的功能，当然也具备`findByEmailAddress`的功能。

> 注意，如果中间的repository接口添加了`@NoRepositoryBean`注解，确认你所有的repository都添加了这个注解这时候spring Data 讲会不会创建实例。

#### 3.3.2. 使用Spring Data多模块来创建Repositories

使用唯一的Spring Data模块在应用中是非常简单，但有时候我们需要多的Spring Data模块，比如：需要定义个Repository去区分两种不同的持久化技术，如果在class path中发现多个Repository时，spring data会进行严格的配置限制，确保每个repository或者实体决定绑定那个Spring Data模块：

1、如果 repository 定义继承特殊的Repository，他是一个特殊的Spring Data模块

2、如果实体注解了一个特殊的声明，它是一个特殊的spring Data模块，spring Data模块接收第三方的声明（例如：JPA's `@Entity`）或者提供来自 Spring Data MonggoDB/Spring Data Elasticsearch的 `@Document` 。

例8. 自定义特殊的Repostity

```java
interface MyRepository extends JpaRepository<User, Long> { }

@NoRepositoryBean
interface MyBaseRepository<T, ID extends Serializable> extends JpaRepository<T, ID> {
  …
}

interface UserRepository extends MyBaseRepository<User, Long> {
  …
}
```

`MyRepository` and  `UserRepository` 继承于 `JpaRepository`在这个层级中是对Spring Data JPA 模块的合法替代

例9. 使用一般的接口定义Repository

```java
interface AmbiguousRepository extends Repository<User, Long> {
 …
}

@NoRepositoryBean
interface MyBaseRepository<T, ID extends Serializable> extends CrudRepository<T, ID> {
  …
}

interface AmbiguousUserRepository extends MyBaseRepository<User, Long> {
  …
}
```
```AmbiguousRepository``和`AmbiguousUserRepository` 仅继承于`Repository`和`CrudRepostory`在他们的层级。当它们使用一个spring data模块的时候是完美的，但是如果使用多模块spring data 是，spirng 无法区分每个Repository的范围。

例10. 使用实体类注解来定义Repository的使用范围

```java
interface PersonRepository extends Repository<Person, Long> {
 …
}

@Entity
public class Person {
  …
}

interface UserRepository extends Repository<User, Long> {
 …
}

@Document
public class User {
  …
}
```

 ```Person```使用了```@Entity``` 注解```PersonRepository```引用了它，所以这个仓库清晰的使用了Sping Data JPA。 ```UserRepository```引用的```User``` 声明了```@Document```表面这个仓库将使用Spring Data MongoDB 模块。

例11. 使用混合的注解来定义仓库
```java
interface JpaPersonRepository extends Repository<Person, Long> {
 …
}

interface MongoDBPersonRepository extends Repository<Person, Long> {
 …
}

@Entity
@Document
public class Person {
  …
}
```

这个例子中实体类```Person···使用了两种注解，表明这个实体类既可以用于```JpaPersonRepository```也可以用于```MongoDBPersonRepository ```，Spring Data不能确定仓库类型导致未定义的行为。

通过Repository继承或者使用注解都是为了确定使用那个Spring Data模块。使用多个注解到同一个实体来达到多类型的持久化技术，Spring Data不在限制只能绑定到一个Repostitory中。 

最后一种方法来区分不同的仓库类型，使用包路径来判断。不同的包路径下的仓库使用不同的仓库类型，通过在配置类```configuration```中声明注解来实现，也可以通过xml配置来定义。

例12： 通过注解来实现不同包路径，使用不同的仓库
```java
@EnableJpaRepositories(basePackages = "com.acme.repositories.jpa")
@EnableMongoRepositories(basePackages = "com.acme.repositories.mongo")
interface Configuration { }
```

















