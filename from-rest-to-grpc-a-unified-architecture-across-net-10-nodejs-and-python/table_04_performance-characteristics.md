# ### Performance Characteristics

| Metric | REST (Fastify) | gRPC (Node.js) | Improvement |
|--------|----------------|----------------|-------------|
| **Throughput** | 15,000 req/sec | 35,000 req/sec | 2.3x |
| **Latency (P99)** | 12 ms | 8 ms | 1.5x |
| **Memory Footprint** | 80 MB | 50 MB | 1.6x |
| **Concurrent Streams** | 500 | 5,000+ | 10x |
| **Data Transfer** | 500 bytes/msg | 50 bytes/msg | 10x |
