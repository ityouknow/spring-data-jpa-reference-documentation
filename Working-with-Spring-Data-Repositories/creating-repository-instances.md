### 4.5. 创建repository实例

在这个部分你创建实例和为repository接口定义的bean。这样做的一个方法是使用Spring的名称空间，这是与每个Spring Data模块，支持存储机制，虽然我们一般建议使用的JAVA配置风格的配置。 


#### 4.5.1 XML配置

每一个Spring Data模块都包含repositories元素能够让你简单的基于base-package定义来进行Spring扫描。

例18 通过XML来开启Spring Data repositories

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans:beans xmlns:beans="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns="http://www.springframework.org/schema/data/jpa"
  xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/data/jpa
    http://www.springframework.org/schema/data/jpa/spring-jpa.xsd">

  <repositories base-package="com.acme.repositories" />

</beans:beans>
```


