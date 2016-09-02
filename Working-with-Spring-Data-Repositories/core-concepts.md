### 4.1核心概念

在Spring Data 接口 Repository 需要domain 以及id类型域类的类型参数。 这个接口行为主要是一个标记接口获取类型一起工作,并帮助您发现接口,扩展这一个 CrudRepository 提供复杂的CRUD功能的实体类进行管理。 

例 3. CrudRepository 接口

``` java
public interface CrudRepository<T, ID extends Serializable>

 extends Repository<T, ID> {        - 1

 <S extends T> S save(S entity);

 T findOne(ID primaryKey);

 Iterable<T> findAll();

 Long count();

 void delete(T entity);

 boolean exists(ID primaryKey);

 // … more functionality omitted.

}

```
 保存指定的泛型的实体。

返回的实体被给定的id。

返回所有实体。

返回的实体的数量。

删除给定的实体。

表示一个实体是否与给定id的存在。
