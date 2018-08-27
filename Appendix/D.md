## 附录 D: Repository 查询返回类型

**支持的查询返回类型**

下表列出了Spring Data repositories通常支持的返回类型。 但是，请参阅特定于存储库的文档以获取支持的返回类型的确切列表，因为某些在这里列出的可能在特定存储中不受支持。

> 空间类型（georesult，georesults、GeoPage）仅用于支持地理信息查询的数据存储。

*表 11. 查询的返回类型*

| 返回类型                   | 描述                                       |
| ---------------------- | ---------------------------------------- |
| `void`                 | 表示没有返回值                                  |
| Primitives             | Java 原语                                  |
| Wrapper types          | Java 的包装类型                               |
| `T`                    | 一个独特的实体。期望查询方法最多返回一个结果。 万一没有结果， `null` 将被返回。 多于一个结果将触发 `IncorrectResultSizeDataAccessException` |
| `Iterator<T>`          | 一个 `Iterator`                            |
| `Collection<T>`        | 一个 `Collection`                          |
| `List<T>`              | 一个 `List`                                |
| `Optional<T>`          | 一个 Java 8 或 Guava 的`Optional`. 期望查询方法最多返回一个结果。 万一没找到结果 `Optional.empty()`/`Optional.absent()` 将被返回。 多于一个结果将触发 `IncorrectResultSizeDataAccessException` |
| `Option<T>`            | Scala 或 JavaSlang 两者中的一个`Option` 类型. 语义上和上面描述的 Java 8的 `Optional` 类型有相同的行为 |
| `Stream<T>`            | 一个Java 8 的 `Stream`                      |
| `Future<T>`            | 一个 `Future`。被 `@Async`注解的方法不可以，并且要求启用Spring的异步方法执行功能 |
| `CompletableFuture<T>` | 一个 Java 8 的 `CompletableFuture`。被 `@Async`注解的方法不可以，并且要求启用Spring的异步方法执行功能 |
| `ListenableFuture`     | 一个 `org.springframework.util.concurrent.ListenableFuture`。 被 `@Async`注解的方法不可以，并且要求启用Spring的异步方法执行功能 |
| `Slice`                | A sized chunk of data with information whether there is more data available. Requires a `Pageable` method parameter. |
| `Page<T>`              | 一个包含额外信息的 `Slice` , 例如返回结果的总数量。需要一个 `Pageable`作为方法的参数 |
| `GeoResult<T>`         | 一个 result entry with additional information, 例如到参考位置的距离 |
| `GeoResults<T>`        | A list of `GeoResult<T>` with additional information, 例如到参考位置的平均距离 |
| `GeoPage<T>`           | A `Page` with `GeoResult<T>`, 例如到参考位置的平均距离 |


