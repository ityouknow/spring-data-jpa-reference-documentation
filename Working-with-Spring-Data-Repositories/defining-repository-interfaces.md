### 4.3 定义repository的接口

首先需要定义实体类的接口，接口必须继承repository并且输入实体类型和ID类型，如果需要用到CRUD方法，可以使用```CrudRepository```来替代```Repository```.

#### 4.3.1 自定义接口
通常,您的存储库接口将会扩展``Repository``, ``CrudRepository``或``PagingAndSortingRepository``。 另外,如果你不想继承Spring Data接口,还可以注释库接口``@RepositoryDefinition``。 扩展 CrudRepository 公开了一套完整的方法来操作您的实体。 如果你喜欢选择调用方法,简单地复制你想要的曝光``CrudRepository``到你的repository。

> 这允许您定义自己的抽象上的弹性提供数据存储库的功能。