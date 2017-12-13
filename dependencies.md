## 第二章 项目依赖

由于spring data依赖于很多不同的组件,其中大部分都有不同的版本号，找到兼容的最简单方式就是利用我们定义的bom模版，在maven项目中，你可以在pom文件中定义这样的片段`<dependencyManagement />`

例1. 在BOM中使用spring data发布的版本

```xml
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>org.springframework.data</groupId>
      <artifactId>spring-data-releasetrain</artifactId>
      <version>${release-train}</version>
      <scope>import</scope>
      <type>pom</type>
    </dependency>
  </dependencies>
</dependencyManagement>
```

最新发布的版本是`Kay-SR1`。名字是按照字母顺序的升序来排列，最新可用的列表在[这里](https://github.com/spring-projects/spring-data-commons/wiki/Release-planning)。版本的命名格式为: `${name}-${release}`,其中`release`是下列5种之一：

* BUILD-SNAPSHOT - 最新的快照

* M1, M2 etc. - 里程碑

* RC1, RC2 etc. - 新版本预发布

* RELEASE - 正式发布的版本

* SR1, SR2 etc. - 服务版本

我们可以在[spring data using bom](https://github.com/spring-projects/spring-data-examples/tree/master/bom)这个链接中查看如何使用BOM模版。

此时，你在对JPA的模块引用中不需要添加版本号，如下。

例2.声明一个JPA的模块引用

```xml
<dependencies>
  <dependency>
    <groupId>org.springframework.data</groupId>
    <artifactId>spring-data-jpa</artifactId>
  </dependency>
<dependencies>
```

### 2.1 使用Spring Boot管理依赖

Spring boot 已经选择了最新的版本，如果你想更新到最新的版本，只需配置`spring-data-releasetrain.version` 选择不同的版本来使用即可。

### 2.2 Spring 框架

当前的Spring Data模版需要依赖Spring框架5.0.1发布版或者更高，也可以使用旧版中修复了bug的版本，但是还是推荐使用最新的版本。

