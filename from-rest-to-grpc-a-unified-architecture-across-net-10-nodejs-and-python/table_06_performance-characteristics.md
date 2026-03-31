# ### Performance Characteristics

| Metric | REST (FastAPI) | gRPC (Python) | Improvement |
|--------|----------------|---------------|-------------|
| **Throughput** | 8,000 req/sec | 18,000 req/sec | 2.25x |
| **Latency (P99)** | 25 ms | 15 ms | 1.67x |
| **Memory Footprint** | 150 MB | 100 MB | 1.5x |
| **ML Inference** | N/A | 5-10 ms | Real-time |
| **Data Transfer** | 500 bytes/msg | 50 bytes/msg | 10x |
