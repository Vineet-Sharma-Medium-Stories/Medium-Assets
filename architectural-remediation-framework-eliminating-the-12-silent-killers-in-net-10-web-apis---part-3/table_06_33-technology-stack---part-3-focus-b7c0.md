# table_06_33-technology-stack---part-3-focus-b7c0


| Layer | Technology | Version | Purpose | Anti-Pattern Addressed |
|-------|------------|---------|---------|------------------------|
| **Rate Limiting** | ASP.NET Core Rate Limiting | 10.0 | Built-in middleware | #10 No Rate Limiting |
| **Distributed Locking** | Redis | 7.2 | Idempotency key coordination | #12 No Idempotency |
| **Caching** | Redis | 7.2 | Store idempotency results | #12 No Idempotency |
| **Idempotency** | Custom Redis Service | N/A | Idempotency key management | #12 No Idempotency |
