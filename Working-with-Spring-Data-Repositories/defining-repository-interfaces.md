### 4.3 定义repository的接口

首先需要定义实体类的接口，接口必须继承repository并且输入实体类型和ID类型，如果需要用到CRUD方法，可以使用```CrudRepository```来替代```Repository```.

#### 4.3.1 自定义接口
通常,您的存储库接口将会扩展``Repository``, ``CrudRepository``或``PagingAndSortingRepository``。 另外,如果你不想继承Spring Data接口,还可以注释库接口``@RepositoryDefinition``。 扩展``CrudRepository``公开了一套完整的方法来操作您的实体。 如果你喜欢选择调用方法,简单地复制你想要的曝光``CrudRepository``到你的repository。

> 这允许您定义自己的抽象上的弹性提供数据存储库的功能。

例7.有选择地公开CRUD方法
``` java
@NoRepositoryBean
interface MyBaseRepository<T, ID extends Serializable> extends Repository<T, ID> {

  T findOne(ID id);

  T save(T entity);
}

interface UserRepository extends MyBaseRepository<User, Long> {
  User findByEmailAddress(EmailAddress emailAddress);
}
``` 

第一步你定义了一个公共基础的接口提供了```findOne(…)```和```save(...)```方法,这些方法将会引入到你选择的spring Data的实现类中，例如JPA：```SimpleJpaRepository```,因为他们匹配```CrudRepository```的方法签名，所以```UserRepository```将会具备 save Users的功能和```findOne(…)```的功能，当然也具备```findByEmailAddress```的功能。

> 注意，如果中间的repository接口添加了```@NoRepositoryBean```注解，确认你所有的repository都添加了这个注解这时候spring Data 讲会不会创建实例。


#### 4.3.2. 使用Spring Data多模块来创建Repositories 

使用唯一的Spring Data模块在应用中是非常简单，但有时候我们需要多的Spring Data模块，比如：需要定义个Repository去区分两种不同的持久化技术



























