# Table 1: Benchmark: Async vs Sync Under Load

| Scenario | Threads Used | Requests/sec | Memory/Request |
|----------|--------------|--------------|----------------|
| Synchronous API | 100 blocked threads | 500 | 2.1 MB |
| Async API (legacy) | 10 threads | 4,500 | 1.8 MB |
| Async API (.NET 10) | 8 threads | 5,200 | 1.2 MB |
