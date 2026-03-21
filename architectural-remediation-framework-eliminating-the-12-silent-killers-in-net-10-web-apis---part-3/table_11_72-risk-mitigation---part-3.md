# table_11_72-risk-mitigation---part-3


| Risk | Probability | Impact | Mitigation Strategy |
|------|------------|--------|---------------------|
| **Redis Failure** | Low | Critical | Redis cluster, connection resilience, fallback to in-memory |
| **Idempotency Key Exhaustion** | Low | Medium | TTL on keys, background cleanup, monitoring |
| **Rate Limiting False Positives** | Medium | Medium | Gradual rollout, whitelist for trusted IPs, monitoring |
| **Client Incompatibility** | Medium | High | Feature flags, backward compatibility, client documentation |
| **Performance Overhead** | Low | Medium | Redis is sub-millisecond, measure and optimize |
