# table_02_database-options-comparison


| Database | Write Perf | Read Perf | Consistency | Scaling | Best For |
|----------|------------|-----------|-------------|---------|----------|
| **Azure SQL (Hyperscale)** | Good | Excellent | Strong | 100TB+ | Transactional writes, complex queries |
| **Cosmos DB** | Excellent | Excellent | Tunable | Global | Global scale, high throughput |
| **Azure SQL + Redis** | Good | Excellent | Eventual | Read scale-out | Read-heavy workloads |
| **SQL + Azure Synapse** | Good | Excellent | Snapshot | Petabyte | Analytics, reporting |
