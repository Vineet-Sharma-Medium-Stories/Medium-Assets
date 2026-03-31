# ### Performance Characteristics

| Metric | REST (ASP.NET Core) | gRPC (.NET 10) | Improvement |
|--------|---------------------|----------------|-------------|
| **Throughput** | 20,000 req/sec | 50,000 req/sec | 2.5x |
| **Latency (P99)** | 15 ms | 5 ms | 3x |
| **Memory Footprint** | 150 MB | 50 MB (AOT) | 3x |
| **Cold Start** | 200 ms | 25 ms | 8x |
| **Data Transfer** | 500 bytes/msg | 50 bytes/msg | 10x |
