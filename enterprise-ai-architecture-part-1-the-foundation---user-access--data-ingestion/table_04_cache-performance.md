# table_04_cache-performance


| Tier | Hit Rate | Latency | Capacity |
|------|----------|---------|----------|
| L1 (Memory) | 20% | <1ms | 1000 items |
| L2 (Redis) | 70% | 5ms | 1M items |
| Miss (Generate) | 10% | 200ms | Unlimited |
