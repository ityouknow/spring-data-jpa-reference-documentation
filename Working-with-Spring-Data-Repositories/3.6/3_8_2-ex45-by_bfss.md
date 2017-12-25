#### 3.8. Spring Data 的拓展

本节记录了一系列Spring Data的扩展，它们可以在各种上下文中启用Spring Data。目前大多数集成都是针对Spring MVC的。

#### 3.8.1. Querydsl 拓展

\[Querydsl\]\(\([http://www.querydsl.com/\)\)是一个框架，它可以通过流畅的API构建静态类型的SQL查询。](http://www.querydsl.com/%29%29是一个框架，它可以通过流畅的API构建静态类型的SQL查询。)

几个Spring Data模块通过提供`QueryDslPredicateExecutor`与Querydsl进行集成。

Example 39. QueryDslPredicateExecutor 接口

```java
public interface QueryDslPredicateExecutor<T> {

  Optional<T> findById(Predicate predicate);  

  Iterable<T> findAll(Predicate predicate);   

  long count(Predicate predicate);            

  boolean exists(Predicate predicate);        

  // … more functionality omitted.
}
```

> 1. 找到并返回匹配到Predicate的一个单个实体
> 2. 找到并返回匹配到Predicate的所有实体
> 3. 返回匹配到Predicate的所有实体的数目
> 4. 返回是否存在匹配到Predicate的实体

在你的respository接口上简单地继承`QueryDslPredicateExecutor`就可以支持使用Querydsl

Example 40. Querydsl在repositories上的集成

```java
interface UserRepository extends CrudRepository<User, Long>, QueryDslPredicateExecutor<User> {

}
```

上述操作可以使你使用Querydsl Predicate s编写类型安全的语句

```java
Predicate predicate = user.firstname.equalsIgnoreCase("dave")
    .and(user.lastname.startsWithIgnoreCase("mathews"));

userRepository.findAll(predicate);
```



**3.8.2. Web 支持**

本节包含的是在Spring Data Commons 1.6中实现的Spring data 中web 支持方面的文档。由于新引入的特性更改了很多内容, 所以如果您想找一些包含了以前行为的说明，请参阅[Legacy web support](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#web.legacy)。

如果程序模块支持repository编程模型,  那么就可以使用Spring Data各种各样的 web 支持。若需启用 web 相关的部分，需要在classpath上添加 spring MVC 的jar文件, 其中一些也提供了与 spring HATEOAS [\[2\]](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#_footnote_2) 的集成。通常情况下, 在 JavaConfig 配置类中使用 `@EnableSpringDataWebSupport` 注解来启用它。

_Example 41. 启用Spring Data web支持_

```java
@Configuration
@EnableWebMvc
@EnableSpringDataWebSupport
class WebConfiguration {}
```

`@EnableSpringDataWebSupport` 注解注册了一些我们下面要讨论的组件。它还会检测classpath上的 Spring HATEOAS, 并为其注册集成组件 \(如果存在的话\)。

或者, 如果您使用的是 XML 配置, 请像示例代码那样将`SpringDataWebSupport` 或 `HateoasAwareSpringDataWebSupport`注册为Spring bean。

Example 42. 在XML中启用Spring Data web支持

```xml
<bean class="org.springframework.data.web.config.SpringDataWebConfiguration" />

<!-- 如果你同时也在使用Spring HATEOAS，那么用下面这行来代替上面的代码-->
<bean class="org.springframework.data.web.config.HateoasAwareSpringDataWebConfiguration" />
```

基础 web 支持

上面显示的配置将注册以下几个基本组件:

* `DomainClassConverter`: 使 Spring MVC 能够从请求参数或路径变量中解析由repository管理的domain类的实例。
* `HandlerMethodArgumentResolver`: 让 Spring MVC 可以从请求参数中解析Pageable和Sort实例。

DomainClassConverter

`DomainClassConverter`允许您直接在 Spring MVC controller方法签名中使用domain类型, 这样您就不必手动从repository查找domain的实例。

_Example 43. 一个在方法签名中使用实体类的Spring MVC controller_

```java
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

如你所见, 该方法直接接收一个User实例, 并没有进行其他的查找操作。首先，Spring MVC将路径变量转换为domain类的id，然后通过在此domain类上注册的repository上调用`findById（...）`方法来访问该实例。

就现在而言, repository必须实现`CrudRepository`才能可以进行转换。

有关Pageable和Sort的HandlerMethodArgumentResolvers

上面的配置代码段还注册了一个 `PageableHandlerMethodArgumentResolver` 以及一个 `SortHandlerMethodArgumentResolver` 的实例。使`Pageable`和`sort`也可以作为controller方法的参数。

_Example 44. 把Pageable作为方法参数_

```java
@Controller
@RequestMapping("/users")
class UserController {

  private final UserRepository repository;

  UserController(UserRepository repository) {
    this.repository = repository;
  }

  @RequestMapping
  String showUsers(Model model, Pageable pageable) {

    model.addAttribute("users", repository.findAll(pageable));
    return "users";
  }
}
```

此方法签名会让 Spring MVC 尝试使用以下默认配置从请求参数中解析Pageable实例:

Table 1. 要计算的Pageable实例的请求参数  
\|\|\|  
----\|:---------------------------  
page\|    要检索的页, 从0开始并默认为0。  
size\|    要检索的页面的大小, 默认为20。  
sort\|    属性应该按照这种格式排序：`property,property(,ASC|DESC)`。默认的排序为升序。如果要需要切换, 请使用多个`sort`参数, 例如：`?sort=firstname&sort=lastname,asc`

若要自定义此行为, 应分别注册一个实现了接口 `PageableHandlerMethodArgumentResolverCustomizer` 或 `SortHandlerMethodArgumentResolverCustomizer` 的 bean。这样它的`customize()` 方法将被调用, 从而允许您更改设置。就像下面这样：

```java
@Bean SortHandlerMethodArgumentResolverCustomizer sortCustomizer() {
    return s -> s.setPropertyDelimiter("<-->");
}
```

如果设置一个现有的`MethodArgumentResolver`的属性不足以达到您的目的，请扩展`SpringDataWebConfiguration`或已启用的HATEOAS的等效项，重写`pageableResolver（）`或`sortResolver（）`方法并导入您的自定义配置文件，而不是使用`@ Enable`注解。

如果需要从请求中解析多个`Pageable`或`Sort`实例 \(例如, 对于多个表的操作\), 则可以使用 Spring 的 `@Qualifier`注解来区分。那么请求参数必须以 `${qualifier}` 为前缀。它看起来像这样:

```java
String showUsers(Model model,
      @Qualifier("foo") Pageable first,
      @Qualifier("bar") Pageable second) { … }
```

你必须自己填充 `foo_page` 和 `bar_page` 等。

传入该方法的默认`Pageable`等价于`new PageRequest(0, 20)`, 但也可以使用`Pageable`参数上的 `@PageableDefault` 注解进行自定义。

对Pageable的超媒体（Hypermedia）支持

Spring HATEOAS附带一个表示模型类`PagedResources`来允许使用必要的`Page`元数据丰富`Page`实例的内容，同时也使用链接让客户端在页面间轻松导航。 将`Page`转换为`PagedResources`这一工作是由实现了Spring HATEOAS 的`ResourceAssembler`接口的`PagedResourcesAssembler`完成的。

例47. 声明一个Jackson库填充器
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

通过检查JSON文档的`_class`属性来确定解组后的JSON对象的类型。





