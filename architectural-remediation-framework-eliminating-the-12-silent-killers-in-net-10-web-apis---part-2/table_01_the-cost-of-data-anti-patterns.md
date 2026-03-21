# table_01_the-cost-of-data-anti-patterns


| Anti-Pattern | Hidden Cost | Production Impact at 3 AM |
|--------------|-------------|---------------------------|
| **No Pagination** | 10,000+ rows transferred per request | Browser crashes, network timeouts |
| **Over-fetching** | 10x more data than needed | Database I/O saturation |
| **EF Entities as Response** | Circular references, sensitive data exposure | JSON serialization errors, security breaches |
| **Wrong Status Codes** | All responses return 200 OK | Frontend cannot distinguish success from error |
