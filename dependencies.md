## 3. 项目依赖

由于spirng data依赖于很多不同的组件,其中大部分都有不同的版本号，找到兼容的最简单方式就是利用我们定义的bom模版，在maven项目中，你可以在pom文件中定义这样的片段```<dependencyManagement />``` 

例1. 在BOM中使用spring data发布的版本
``` xml
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

最新发布的版本是```Hopper-SR2```。名字是按照字母顺序的升序来排列，最新可用的列在[这里](https://github.com/spring-projects/spring-data-commons/wiki/Release-planning)。版本的命名格式为: ```${name}-${release}```发布的可能是下列之一：

- BUILD-SNAPSHOT - current snapshots
- M1, M2 etc. - milestones
- RC1, RC2 etc. - release candidates
- RELEASE - GA release
- SR1, SR2 etc. - service releases
