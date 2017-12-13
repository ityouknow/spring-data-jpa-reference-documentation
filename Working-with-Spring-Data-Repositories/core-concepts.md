### 3.1. 核心概念

Spring Data库的核心接口是`Repository`。它使用domain类去管理，domain类中的id类型作为类型参数。这个接口主要作为一个标记接口，依靠具体的类型运作并帮助您发现接口，`CrudRepository` 提供丰富的CRUD功能去管理实体类。

例 3. CrudRepository 接口

```java
public interface CrudRepository<T, ID extends Serializable>

 extends Repository<T, ID> {        

 <S extends T> S save(S entity);    (1)

 T findOne(ID primaryKey);          (2)

 Iterable<T> findAll();             (3)

 Long count();                      (4)

 void delete(T entity);             (5)

 boolean exists(ID primaryKey);     (6)

 // … more functionality omitted.

}
```

\(1\) 保存给定的实体。

\(2\) 返回给定id的实体。

\(3\) 返回所有实体。

\(4\) 返回实体的数量。

\(5\) 删除给定的实体。

\(6\) 表明一个指定id的实体是否存在。

> 我们还提供持久性特定于技术的抽象如: `JpaRepository`或 `MongoRepository`. 这些接口继承于`CrudRepository`，实现了特定的一些功能

`CrudRepository`有一个`PagingAndSortingRepository` 抽象,增加了额外的方法来简化对实体的分页访问:

例4： PagingAndSortingRepository

```java
public interface PagingAndSortingRepository<T, ID extends Serializable>
  extends CrudRepository<T, ID> {

  Iterable<T> findAll(Sort sort);

  Page<T> findAll(Pageable pageable);
}
```

进入`用户类别`的第二页（每一页的条目是20），可以照下面这样来分页

```java
PagingAndSortingRepository<User, Long> repository = // … get access to a bean
Page<User> users = repository.findAll(new PageRequest(1, 20));
```
除了查询方法外，还有统计查询和删除查询。

例5 查询并统计

```java
public interface UserRepository extends CrudRepository<User, Long> {

  Long countByLastname(String lastname);
}
```

例6 查询并删除

```java
public interface UserRepository extends CrudRepository<User, Long> {

  Long deleteByLastname(String lastname);

  List<User> removeByLastname(String lastname);

}
```



