## 附录 C: Repository 查询关键词

### 支持的查询关键词

下表列出了Spring Data repository查询派生机制支持的关键字。但是，请参考特定的存储库的文档来获得支持的关键字的确切列表，因为在这里列出的某些目录可能在特定的存储库中不受支持。

| 逻辑关键词                 | 关键词表达式                                   |
| --------------------- | ---------------------------------------- |
| `AND`                 | `And`                                    |
| `OR`                  | `Or`                                     |
| `AFTER`               | `After`, `IsAfter`                       |
| `BEFORE`              | `Before`, `IsBefore`                     |
| `CONTAINING`          | `Containing`, `IsContaining`, `Contains` |
| `BETWEEN`             | `Between`, `IsBetween`                   |
| `ENDING_WITH`         | `EndingWith`, `IsEndingWith`, `EndsWith` |
| `EXISTS`              | `Exists`                                 |
| `FALSE`               | `False`, `IsFalse`                       |
| `GREATER_THAN`        | `GreaterThan`, `IsGreaterThan`           |
| `GREATER_THAN_EQUALS` | `GreaterThanEqual`, `IsGreaterThanEqual` |
| `IN`                  | `In`, `IsIn`                             |
| `IS`                  | `Is`, `Equals`, (or no keyword)          |
| `IS_EMPTY`            | `IsEmpty`, `Empty`                       |
| `IS_NOT_EMPTY`        | `IsNotEmpty`, `NotEmpty`                 |
| `IS_NOT_NULL`         | `NotNull`, `IsNotNull`                   |
| `IS_NULL`             | `Null`, `IsNull`                         |
| `LESS_THAN`           | `LessThan`, `IsLessThan`                 |
| `LESS_THAN_EQUAL`     | `LessThanEqual`, `IsLessThanEqual`       |
| `LIKE`                | `Like`, `IsLike`                         |
| `NEAR`                | `Near`, `IsNear`                         |
| `NOT`                 | `Not`, `IsNot`                           |
| `NOT_IN`              | `NotIn`, `IsNotIn`                       |
| `NOT_LIKE`            | `NotLike`, `IsNotLike`                   |
| `REGEX`               | `Regex`, `MatchesRegex`, `Matches`       |
| `STARTING_WITH`       | `StartingWith`, `IsStartingWith`, `StartsWith` |
| `TRUE`                | `True`, `IsTrue`                         |
| `WITHIN`              | `Within`, `IsWithin`                     |
