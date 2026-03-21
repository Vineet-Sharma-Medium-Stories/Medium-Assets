# table_02_most-net-applications-fail-not-because-of-complex-685a


| Anti-Pattern | Silent Killer Mechanism | Production Symptom |
|--------------|------------------------|-------------------|
| **Fat Controllers** | Business logic scattered across HTTP layer | Impossible to test, every change breaks something |
| **No Validation** | Invalid data enters the system | Data corruption, unexpected exceptions |
| **Raw Exceptions** | Internal details exposed to clients | Security vulnerabilities, confused API consumers |
| **Blocking Async** | Thread pool starvation | System collapses under moderate load |
| **No Cancellation** | Resources wasted on abandoned requests | DB connection pool exhaustion, memory leaks |
| **No Observability** | Blind debugging | Hours to diagnose issues, reactive alerting |
