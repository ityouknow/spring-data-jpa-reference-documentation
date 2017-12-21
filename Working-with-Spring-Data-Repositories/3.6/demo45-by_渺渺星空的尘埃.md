超媒体支持Pageables

Spring HATEOAS提供了一个表示模型类`PagedResources`，它允许`Page`使用必要的`Page`元数据丰富实例的内容，以及让客户端轻松浏览页面的链接。页面到a的转换`PagedResources`是通过Spring HATEOAS`ResourceAssembler`接口的实现完成的`PagedResourcesAssembler`。

示例45.使用PagedResourcesAssembler作为控制器方法参数

```java
@Controller
class PersonController {

@Autowired PersonRepository repository;

@RequestMapping(value = "/persons", method = RequestMethod.GET)
HttpEntity<PagedResources<Person>> persons(Pageable pageable,
PagedResourcesAssembler assembler) {

Page<Person> persons = repository.findAll(pageable);
return new ResponseEntity<>(assembler.toResources(persons), HttpStatus.OK);
}
}
```

如上所示启用配置允许将PagedResourcesAssembler其用作控制器方法参数。调用toResources\(…\)它将导致以下内容：

* 页面的内容将成为`pagedresources`实例的内容。
* `pagedresources`会得到一个`PageMetadata`附加填充信息形成的实例Page和基础PageRequest。
* 取决于页面状态的`PagedResources`获取prev和next链接。链接将指向调用的方法映射到的URI。添加到该方法的分页参数将与该设置相匹配，`PageableHandlerMethodArgumentResolver`以确保以后可以解析链接。

假设我们在数据库中有30个Person实例。你现在可以触发一个请求，你会看到类似的东西：GET [http://localhost:8080/persons](http://localhost:8080/persons)

```
{ "links" : [ { "rel" : "next",
"href" :"http://localhost:8080/persons?page=1&size=20 }
],
"content" : [
… // 20 Person instances rendered here
],
"pageMetadata" : {
"size" : 20,
"totalElements" : 30,
"totalPages" : 2,
"number" : 0
}
}
```

您会看到汇编程序生成了正确的URI，并且还提取了缺省配置，将参数解析`Pageable`为一个即将到来的请求。这意味着，如果您更改该配置，链接将自动遵守更改。默认情况下，汇编程序指向它被调用的控制器方法，但是可以通过交付定制的定制Link来作为基础来构建分页链接以重载`PagedResourcesAssembler.toResource(…)`方法。

##### {#core.web.type-safe}

##### Querydsl网络支持 {#core.web.type-safe}

对于具有[QueryDSL](http://www.querydsl.com/)集成的商店，可以从包含在`Request`查询字符串中的属性派生查询。

这意味着给定User来自以前样本的对象一个查询字符串

`?firstname=Dave&lastname=Matthews?firstname=Dave&lastname=Matthews`

可以解决

`QUser.user.firstname.eq("Dave").and(QUser.user.lastname.eq("Matthews"))`

使用`QuerydslPredicateArgumentResolver`。

> @EnableSpringDataWebSupport当在类路径上找到Querydsl时， 该功能将自动启用。

添加一个`@QuerydslPredicate`方法签名将提供一个准备使用`Predicate`，可以通过执行`QueryDslPredicateExecutor`。

> 类型信息通常从方法返回类型中解析出来。由于这些信息不一定与域类型匹配，因此使用root属性可能是一个好主意QuerydslPredicate。

```
@Controller
class UserController {

@Autowired UserRepository repository;

@RequestMapping(value = "/", method = RequestMethod.GET)
String index(Model model, @QuerydslPredicate(root = User.class) Predicate predicate,
Pageable pageable, @RequestParam MultiValueMap<String, String> parameters) {

model.addAttribute("users", repository.findAll(predicate, pageable));

return "index";
}
}
```

| 解析查询字符串参数匹配Predicate的User。 |
| :--- |


默认绑定如下：

* Object简单的属性如eq。
* Object就像集合的属性一样contains。
* Collection简单的属性如in。

这些绑定可以通过Java 8 的`bindings`属性`@QuerydslPredicate`或通过使用Java 8 `default methods`添加`QuerydslBinderCustomizer`到存储库接口来定制。

```java
interface UserRepository extends CrudRepository<User, String>,
QueryDslPredicateExecutor<User>,
QuerydslBinderCustomizer<QUser> {

@Override
default void customize(QuerydslBindings bindings, QUser user) {

bindings.bind(user.username).first((path, value) -> path.contains(value))
bindings.bind(String.class)
.first((StringPath path, String value) -> path.containsIgnoreCase(value));
bindings.excluding(user.password);
}
}
```

* `QueryDslPredicateExecutor`提供对特定查找方法的访问`Predicate`。
* `QuerydslBinderCustomizer`定义在版本库界面上会自动拾取和快捷方式`@QuerydslPredicate(bindings=…​)`。

* 定义该`username`属性的绑定是一个简单的包含绑定。

* 将`String`属性的默认绑定定义为不区分大小写包含匹配项。

* 从解决方案中排除密码属性`Predicate`。


