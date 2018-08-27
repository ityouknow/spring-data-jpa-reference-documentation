### 3.7. 从聚合根处发布事件

由respositories管理的实体是聚合根。 在“域驱动设计”的应用程序中，这些聚合根通常用来发布域事件。 Spring Data提供了一个注解@DomainEvents，您可以在聚合根的方法上使用该方法使该发布尽可能地简单。

Example 38. Exposing domain events from an aggregate root

```
class AnAggregateRoot {

    @DomainEvents 
    Collection<Object> domainEvents() {
        // … return events you want to get published here
    }

    @AfterDomainEventPublication 
    void callbackMethod() {
       // … potentially clean up domain events list
    }
}
```

> 1. 使用@DomainEvents的方法可以返回单个事件实例或一组事件。 它不能有任何争论。
> 2. 在所有事件发布之后，用@AfterDomainEventPublication注解的方法。 例如 可以用来潜在地清理要发布的事件列表。

每次Spring Data repository的`save(…)`这个方法启动时，这个方法都会启动。

