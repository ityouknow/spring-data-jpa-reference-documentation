### 4.1核心概念

在Spring Data 接口 Repository 需要domain 以及id类型域类的类型参数。 这个接口行为主要是一个标记接口获取类型一起工作,并帮助您发现接口,扩展这一个 CrudRepository 提供复杂的CRUD功能的实体类进行管理。 

例 3. CrudRepository 接口

``` java
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
(1) 保存指定的泛型的实体。

(2) 返回的实体被给定的id。

(3) 返回所有实体。

(4) 返回的实体的数量。

(5) 删除给定的实体。

(6) 表示一个实体是否与给定id的存在。


>我们还提供持久性特定于技术的抽象如: ```JpaRepository ```或 ```MongoRepository```. 这些接口继承于```CrudRepository```，实现了特定的一些功能

```CrudRepository```有一个```PagingAndSortingRepository```抽象,增加了额外的方法来缓解分页的访问实体:

例4： PagingAndSortingRepository
``` java
public interface PagingAndSortingRepository<T, ID extends Serializable>
  extends CrudRepository<T, ID> {

  Iterable<T> findAll(Sort sort);

  Page<T> findAll(Pageable pageable);
}
```
