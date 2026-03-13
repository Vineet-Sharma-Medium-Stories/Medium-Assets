# Table 2: Benchmark: Allocation Patterns

| Pattern | Operations/sec | Allocated | GC Collections |
|---------|---------------|-----------|----------------|
| List without capacity | 1,200,000 | 2.4 MB | Gen0: 15, Gen1: 5 |
| List with capacity | 2,100,000 | 1.2 MB | Gen0: 5, Gen1: 1 |
| Array pooling | 3,500,000 | 0.1 MB | Gen0: 0, Gen1: 0 |
