# Mermaid Diagram 2: What Changes in .NET 10:

```mermaid
graph TD
    A[LINQ Query: context.Orders.Select(o => new OrderDto { Id = o.Id, Total = o.Items.Sum(i => i.Price) })] --> B[.NET 10 Compiled Query Feature];
    B --> C{First Execution?};
    C -->|Yes| D[Compile Expression Tree to IL];
    D --> E[Cache Compiled Delegate];
    E --> F[Execute Against Database];
    C -->|No| F;
    F --> G[Return Projected Results];
    
    style B fill:#f9f,stroke:#333,stroke-width:2px
    style D fill:#ccf,stroke:#333,stroke-width:2px
```
