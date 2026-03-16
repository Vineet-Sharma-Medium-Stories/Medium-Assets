# table_05_search-performance


| Collection Size | Index Type | Latency (p95) | Recall@10 |
|-----------------|------------|----------------|-----------|
| 100k chunks | HNSW | 10ms | 0.99 |
| 1M chunks | HNSW | 25ms | 0.98 |
| 10M chunks | HNSW + Filtering | 50ms | 0.97 |
| 100M chunks | Distributed | 100ms | 0.95 |
