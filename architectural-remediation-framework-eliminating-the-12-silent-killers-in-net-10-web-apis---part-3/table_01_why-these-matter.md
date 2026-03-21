# table_01_why-these-matter


| Anti-Pattern | Silent Killer Mechanism | Production Symptom at 3 AM |
|--------------|------------------------|---------------------------|
| **No Rate Limiting** | API open to unlimited requests | DDoS, resource exhaustion, brute force attacks |
| **No Idempotency** | Retries create duplicate records | Double charges, duplicate orders, data corruption |
