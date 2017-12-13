### 3.2. 查询方法

标准的CRUD功能存储库通常对底层数据存储查询。Spring Data把这些查询变成了四个步骤的过程:

1、声明一个接口继承Repository或其子类，输入实体类型和ID类型。
``` java
interface PersonRepository extends Repository<User, Long> { … }
```
2、在接口里声明查询方法。
``` java
interface PersonRepository extends Repository<Person, Long> {
  List<Person> findByLastname(String lastname);
}
```
3、为这些接口创建代理实例，也可通过 JavaConfig :
``` java
import org.springframework.data.jpa.repository.config.EnableJpaRepositories;

@EnableJpaRepositories
class Config {}
```

或通过xml配置

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
本例中使用了JPA名称空间。如果您正在使用repository中的抽象为任何其他数据源,你需要改变这种适当的名称空间声明你的存储模块来与jpa支持,例如:`mongodb`。 

注意，不用通过Java变量来配置包，默认情况下回根据注解的类来自动声明。定制的包扫描可以使用```basePackage```属性，特定的库可以使用```@Enable```来注解。

4、获得repository 实例注入并使用它。
``` java
class SomeClient {

  @Autowired
  private final PersonRepository repository;
  
  SomeClient(PersonRepository repository) {
      this.repository = repository;
  }

  public void doSomething() {
    List<Person> persons = repository.findByLastname("Matthews");
  }
}
```
接下来的小节详细解释每一个步骤。 


