# Mermaid Diagram 1: What Changes in .NET 10:

```mermaid
graph TD
    A[LINQ Query: context.Products.Where(p => p.Price > 100 && p.Category == "Electronics")] --> B[.NET 10 Expression Visitor];
    B --> C[Normalizes Expression Tree];
    C --> D[Removes Redundant Sub-expressions];
    D --> E[Optimizes Constant Folding];
    E --> F[EF Core Translator];
    F --> G[Generated SQL: SELECT * FROM Products WHERE Price > 100 AND Category = 'Electronics'];
    
    style B fill:#f9f,stroke:#333,stroke-width:2px
    style G fill:#ccf,stroke:#333,stroke-width:2px
```
