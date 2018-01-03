4.3节
声明接口

要执行这些命名查询，您只需要按如下方式指定`UserRepository`：

*Example 54. UserRepsoitory中查询方法的声明*
```java
public interface UserRepository extends JpaRepository<User, Long> {

  List<User> findByLastname(String lastname);

  User findByEmailAddress(String emailAddress);
}
```

Spring Data会尝试将对这些方法的调用解析为一个以配置好的domain类的简单名称开始，接着是用句点分隔的方法名称的命名查询。 所以这里的示例将使用上面定义好的命名查询，而不是试图通过方法名称创建一个新查询。

#### 4.3.4. 使用 @Query

使用命名查询来声明对实体的查询是一个有效的方法，尤其对少量查询来说。 由于查询本身与执行它们的Java方法绑定，所以实际上你可以直接使用Spring Data JPA 的`@Query`注解来绑定它们，而不是将它们注释到domain类中。 这将从持久性特定信息中解放domain类，并将查询一起放置到repository接口中。

对查询方法进行注解的查询优先于使用`@NamedQuery`定义的查询或者在`orm.xml`中声明的命名查询。

*Example 55. 使用@Query在查询方法中声明一个查询*
```java
public interface UserRepository extends JpaRepository<User, Long> {

  @Query("select u from User u where u.emailAddress = ?1")
  User findByEmailAddress(String emailAddress);
}
```

使用高级的 `LIKE` 表达式

使用@Query手动定义查询的查询执行机制允许您在查询定义内定义高级的`LIKE`表达式。

*Example 56. 在@Query中的高级like表达式*
```java
public interface UserRepository extends JpaRepository<User, Long> {

  @Query("select u from User u where u.firstname like %?1")
  List<User> findByFirstnameEndsWith(String firstname);
}
```

在刚刚的示例中，`LIKE`分隔符`％`会被识别出来，之后这个查询会转换为有效的JPQL查询（这将移除`％`符号）。当查询执行时, 传入方法的参数会填充到之前识别到的`LIKE`模型中。

原生查询

`@Query`注解允许通过将`nativeQuery`标志设置为true来执行原生查询。

*Example 57. 在查询方法中使用@Query来声明一个原生查询*
```java
public interface UserRepository extends JpaRepository<User, Long> {

  @Query(value = "SELECT * FROM USERS WHERE EMAIL_ADDRESS = ?1", nativeQuery = true)
  User findByEmailAddress(String emailAddress);
}
```

请注意，我们目前不支持为原生查询执行动态排序，因为我们必须操作已经声明的实际查询，同时我们无法可靠地为原生SQL执行此操作。 但是，您可以通过自己指定count查询来使用原生查询进行分页。

*Example 58. 使用@Query在查询方法中为分页声明原生count查询*
```java
public interface UserRepository extends JpaRepository<User, Long> {

  @Query(value = "SELECT * FROM USERS WHERE LASTNAME = ?1",
    countQuery = "SELECT count(*) FROM USERS WHERE LASTNAME = ?1",
    nativeQuery = true)
  Page<User> findByLastname(String lastname, Pageable pageable);
}
```

这也适用于通过将后缀`.count`添加到你的查询的副本的命名原生查询。 但是要注意的是，您可能必须为您的count查询注册一个结果集映射。

#### 4.3.5. 使用Sort

排序操作可以通过提供一个`PageRequest`或直接使用`Sort`来完成。 `Sort`的`Order`实例中实际使用的属性需要与您的domain模型匹配，这意味着它们会被解析为查询中使用的属性或别名。 JPQL将其定义为*state_field_path_expression*。

>使用任何不可引用的路径表达式都会导致异常。

然而与`@Query`一起使用`Sort`有可能让你不知不觉地在`ORDER BY`子句中使用包含函数调用且没有路径检查的`Order`实例。这种情况很可能发生的，因为`Order`仅仅附加在给定的查询字符串中。 默认情况下，我们将拒绝任何包含函数调用的`Order`实例，但是您可以使用`JpaSort.unsafe`来添加潜在的不安全的排序。

*Example 59. 使用 Sort 和 JpaSort*
```java
public interface UserRepository extends JpaRepository<User, Long> {

  @Query("select u from User u where u.lastname like ?1%")
  List<User> findByAndSort(String lastname, Sort sort);

  @Query("select u.id, LENGTH(u.firstname) as fn_len from User u where u.lastname like ?1%")
  List<Object[]> findByAsArrayAndSort(String lastname, Sort sort);
}

repo.findByAndSort("lannister", new Sort("firstname")); 1.            
repo.findByAndSort("stark", new Sort("LENGTH(firstname)")); 2.    
repo.findByAndSort("targaryen", JpaSort.unsafe("LENGTH(firstname)")); 3. 
repo.findByAsArrayAndSort("bolton", new Sort("fn_len")); 4.   
```

1. 指向domain模型属性的有效`Sort`表达式
2. 包含函数调用的无效`Sort`表达式，将抛出异常
3. 包含显式的不安全的`Order`的有效`Sort`表达式
4. 指向别名函数的有效`Sort`表达式