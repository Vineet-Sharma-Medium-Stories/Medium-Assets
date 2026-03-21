# table_02_most-api-performance-issues-stem-not-from-complex-b688


| Anti-Pattern | Silent Killer Mechanism | Production Symptom |
|--------------|------------------------|-------------------|
| **No Pagination** | Entire tables serialized | Browser memory overflow, API timeouts |
| **Wrong Status Codes** | All responses return 200 OK | Frontend cannot distinguish success from error |
| **Over-fetching** | SELECT * queries | Database CPU saturation, network latency |
| **EF Entities as Response** | Domain models exposed | Circular reference errors, security leaks |
