### 定制一个单独的 repositories
为了使用自定义功能充实repository，首先定义自定义一个接口片段并去实现这个自定义功能。
然后，让你的的repository接口继承那些片段接口。

###### 例 25.自定义实用的repository接口
~~~java
  interface CustomizedUserRepository{
    void someCustomMethod(User user);
  }
~~~
##### 例 26.实现自定义的repository功能
~~~java
  class CustomizedUserRepositoryImpl implements CustomizedUserRepository{
    public void someCustomMethod(User user){
        //你自己定义的实现
    }
  }
~~~
~~~note
与CustomizedUserRepository 接口相比，CustomizedUserRepositoryImpl 类最重要的一点是他的后缀Impl名

~~~
实现本身不依赖于Spring Date，也可以是一个常规的Spring bean
所以，你可以使用标准的依赖注入行为将引用其他beans如JdbcTemplate，参与方面，等等。
##### 27.更改你的repository 接口
~~~java
  interface UserRepository extends CrudRepository<User, Long>,CustomizedUserRepository{
    // 在这里声明一下你的查询方法
  }
~~~
让您的repository 接口继承一个片段。 这样做结合了CRUD和自定义功能，并使其可供客户使用。

Spring Data repositories通过使用构成repository组合的片段来实现。 片段是基础的repository，功能方面如QueryDsl和自定义接口以及它们的实现。 
每次将接口添加到repository 接口时，都可以通过添加片段来增强组合。 每个Spring Data模块提供基础的repository和repository方面的实现。
##### 28.片段及其实现
~~~java
  interface HumanRepository{
    void someHumanMethod(User user);
  }
  
  class HumanRepositoryImpl implements HumanRepository{
    //你的自定义实现
  }
  
  interface EmployeeRepository{
    void someEmployeeMethod(User user);
    
    User anotherEmployeeMethod(User user);
  }
  
  class ContactRepositoryImpl implements ContactRepository{
    public void someContactMethod(User user){
         //你的自定义实现
    }
    
    public User anotherContactMethod(User user){
        //你的自定义实现
    }
  }
~~~

##### 例29.改变你的repository接口
~~~java
  interface UserRepository extends CrudRepository<User, Long>, HumanRepository, ContactRepository {

    // 在这里声明你的查询方法
~~~
repository可能由多个自定义实现组成，这些自定义实现按照其声明的顺序导入。 自定义实现比基本实现和repository方面具有更高的优先级。 
这种排序允许您覆盖基本repository的方面方法，并解决两个片段提供相同的方法签名时的歧义。 repository片段不限于在单个repository接口中使用。
 多个repository可以使用片段接口去重复使用自定义的接口去访问不同的repositories
##### 30.片段覆盖 sava(...)
~~~java
  interface CustomizedSave<T> {
    <S extends T> S save(S entity);
  }

  class CustomizedSaveImpl<T> implements CustomizedSave<T> {

  public <S extends T> S save(S entity) {
    // 你的自定义实现
  }
}

~~~
##### 31.自定义的repository 接口
~~~java
  interface UserRepository extends CrudRepository<User, Long>, CustomizedSave<User> {
  
  }

  interface PersonRepository extends CrudRepository<Person, Long>, CustomizedSave<Person> {
  
  }
~~~
###### 配置
如果使用命令空间来配置，则repository基础结构会尝试通过扫描我们找到repository所在的包中的类来自动检测自定义实现片段。
这些类需要遵循将名称空间元素的属性repository-impl-postfix附加到所找到的命名约定 片段接口名称。 这个后缀默认为Impl。

##### 32.配置样例
第一个配置示例将尝试查找类com.acme.repository.CustomizedUserRepositoryImpl作为自定义repository的实现，
而第二个示例将尝试查找com.acme.repository.CustomizedUserRepositoryFooBar。
#####解决分歧
如果在不同的包中找到具有匹配的类名的多个实现，则Spring Data使用bean名称来标识要使用的正确的名称。

鉴于上面介绍的CustomizedUserRepository的以下两个自定义实现，第一个实现将被选中。 它的bean名称是customUserRepositoryImpl，
匹配分片接口（CustomizedUserRepository）加上后缀Impl。

~~~xml
<repositories base-package="com.acme.repository" />

<repositories base-package="com.acme.repository" repository-impl-postfix="FooBar" />
~~~

### 33.Amibiguous实现的解决方案
~~~java
package com.acme.impl.one;

  class CustomizedUserRepositoryImpl implements CustomizedUserRepository {

    // 你的实现
  }
~~~
~~~java
package com.acme.impl.two;

  @Component("specialCustomImpl")
  class CustomizedUserRepositoryImpl implements CustomizedUserRepository {

    // 你的实现
  }
~~~
如果使用@Component("specialCustom")对UserRepository接口进行注释，
那么bean名称加上Impl将与com.acme.impl.two中为repository实现定义的名称相匹配，并且将被选中而不是第一个。
##### 手动接线
如果您的自定义实现仅使用基于注释的配置和自动装配，那么刚刚显示的方法就可以很好地工作，因为它将被视为任何其他Spring bean。
如果您的实现片段bean需要特殊的方式，您只需声明这个bean并按照上述约定进行命名即可。 
然后，基础架构将通过名称引用手动定义的bean定义，而不是自己创建一个。
##### 34. 自己手动写的自定义实现
~~~xml
  <repositories base-package="com.acme.repository" />

  <beans:bean id="userRepositoryImpl" class="…">
    <!-- further configuration -->
  </beans:bean>
~~~