# table_05_33-sql-query-comparison


| Approach | Generated SQL | Data Transferred | Query Time |
|----------|--------------|------------------|------------|
| **Anti-Pattern** | `SELECT * FROM Orders o LEFT JOIN OrderItems i ON o.Id = i.OrderId LEFT JOIN Products p ON i.ProductId = p.Id LEFT JOIN Customers c ON o.CustomerId = c.Id` | All columns from 4 tables, all rows | 2.5 seconds |
| **Fixed** | `SELECT o.Id, o.OrderNumber, o.OrderDate, o.Total, o.Status FROM Orders o WHERE o.CustomerId = @p0 AND o.IsDeleted = 0 ORDER BY o.OrderDate DESC OFFSET @p1 ROWS FETCH NEXT @p2 ROWS ONLY` | Only needed columns, paginated rows | 50 ms |
