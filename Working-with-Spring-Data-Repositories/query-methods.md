### 4.2 查询方法

标准的CRUD功能存储库通常对底层数据存储查询。Spring Data把这些查询变成了四个步骤的过程:

1、声明一个接口扩展库和类型它或者它的一个个子域类和ID类型,它将处理。
``` java
interface PersonRepository extends Repository<User, Long> { … }
```
2、接口上新建条件查询的方法。
``` java
interface PersonRepository extends Repository<Person, Long> {
  List<Person> findByLastname(String lastname);
}
```
3、为这些接口创建代理实例，可以通过 JavaConfig :
``` java
import org.springframework.data.jpa.repository.config.EnableJpaRepositories;

@EnableJpaRepositories
class Config {}
```

或者xml配置

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xmlns:jpa="http://www.springframework.org/schema/data/jpa"
   xsi:schemaLocation="http://www.springframework.org/schema/beans
     http://www.springframework.org/schema/beans/spring-beans.xsd
     http://www.springframework.org/schema/data/jpa
     http://www.springframework.org/schema/data/jpa/spring-jpa.xsd">

   <jpa:repositories base-package="com.acme.repositories"/>

</beans>
```
在本例中使用的JPA名称空间。如果您正在使用repository中的抽象为任何其他数据源,你需要改变这种适当的名称空间声明你的存储模块来与jpa支持,例如,mongodb。 

Also, note that the JavaConfig variant doesn’t configure a package explictly as the package of the annotated class is used by default. To customize the package to scan use one of the basePackage… attribute of the data-store specific repository @Enable…-annotation.

4、获得repository 实例注入并使用它。
``` java
public class SomeClient {

  @Autowired
  private PersonRepository repository;

  public void doSomething() {
    List<Person> persons = repository.findByLastname("Matthews");
  }
}
```
下来的小节详细解释每一个步骤。 


