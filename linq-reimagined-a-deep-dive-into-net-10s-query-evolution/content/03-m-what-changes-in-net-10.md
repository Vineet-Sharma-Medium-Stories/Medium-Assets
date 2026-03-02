# Mermaid Diagram 3: What Changes in .NET 10:

```mermaid
graph TD
    A[LINQ Query: context.Orders.GroupBy(o => o.CustomerId).Select(g => new { Customer = g.Key, Count = g.Count() })] --> B[EF Core 10 Translator];
    B --> C[Analyzes Grouping Pattern];
    C --> D[Optimizes for SQL Translation];
    D --> E{Can Translate Fully?};
    E -->|Yes| F[Generate SQL with GROUP BY];
    E -->|No| G[Split Query: Group in Memory];
    F --> H[SELECT CustomerId, COUNT(*) FROM Orders GROUP BY CustomerId];
    
    style B fill:#f9f,stroke:#333,stroke-width:2px
    style H fill:#ccf,stroke:#333,stroke-width:2px
```
