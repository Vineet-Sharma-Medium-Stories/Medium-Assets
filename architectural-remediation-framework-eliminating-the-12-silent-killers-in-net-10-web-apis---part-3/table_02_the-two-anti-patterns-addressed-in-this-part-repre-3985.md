# table_02_the-two-anti-patterns-addressed-in-this-part-repre-3985


| Anti-Pattern | Solution | Protection Level | Business Impact |
|--------------|---------|------------------|-----------------|
| **No Rate Limiting** | Built-in ASP.NET Core rate limiting | Prevents abuse, ensures fair usage | Prevents DDoS, protects resources |
| **No Idempotency** | Redis-based idempotency keys | Safe retries, no duplicate processing | Prevents double charges, data corruption |
