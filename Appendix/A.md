## 附录A：命名空间参考

### **&lt;repositories /&gt;元素** {#populator.namespace-dao-config}

该`<repositories />`元素触发了Spring Data存储库基础设施的设置。最重要的属性是base-package定义了用于扫描Spring Data repository接口的包

表8.属性

| **名称** | **描述** |
| :--- | :--- |
| base-package | 定义在自动检测模式下用于扩展库信息的库（实际接口由特定的Spring Data模块确定）。在配置的软件包下面的所有软件包也将被扫描。通配符是允许的。 |
| repository-impl-postfix | 定义后缀以自动检测自定义存储库实现。名称以配置的后缀结尾的类将被视为候选。默认为Impl。 |
| query-lookup-strategy | 确定要用于创建查找器[查询的策略](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#repositories.query-methods.query-lookup-strategies)。详细信息请参阅查询查询策略。默认为`create-if-not-found`。 |
| named-queries-location | 定义查找包含外部定义的查询的属性文件的位置。 |
| consider-nested-repositories | 控制是否应考虑嵌套存储库接口定义。默认为`false`。 |