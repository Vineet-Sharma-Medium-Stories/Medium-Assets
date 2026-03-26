# table_01_whats-changed-from-before-net-10-b0e8


| Feature | Before .NET 10 | .NET 10 |
|---------|---------------|---------|
| **Minimal API Filters** | `IEndpointFilter` interface (different pattern) | `AddEndpointFilter<T>` with same filters as controllers |
| **Filter Registration** | Separate patterns for controllers vs. minimal | Unified registration system |
| **Async Support** | Good, but inconsistent | Fully unified `IAsyncActionFilter` etc. |
| **Dependency Injection** | Required `ServiceFilterAttribute` | Works seamlessly in both styles |
| **Reactive Integration** | Manual implementation | Built-in support with improved Activity APIs |
