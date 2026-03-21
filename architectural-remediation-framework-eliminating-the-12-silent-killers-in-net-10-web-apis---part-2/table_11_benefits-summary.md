# table_11_benefits-summary


| Aspect | Before (SELECT *) | After (Projections) | Improvement |
|--------|-------------------|---------------------|-------------|
| **Columns Returned** | 50 columns | 6 columns | **88%** reduction |
| **Data Read from Disk** | 10 MB | 0.5 MB | **95%** reduction |
| **Network Transfer** | 50 MB | 50 KB | **99.9%** reduction |
| **Memory Allocation** | 500 MB | 5 MB | **99%** reduction |
| **Serialization Time** | 500 ms | 10 ms | **98%** faster |
