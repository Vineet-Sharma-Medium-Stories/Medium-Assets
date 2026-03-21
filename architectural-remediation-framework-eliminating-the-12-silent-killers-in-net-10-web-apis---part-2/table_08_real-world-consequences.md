# table_08_real-world-consequences


| Violation | Impact | Real Example from the Incident |
|-----------|--------|--------------------------------|
| **200 for errors** | Monitoring blind | No alerts when database down |
| **200 for not found** | Confusing logs | 404 not logged |
| **200 for validation** | Poor UX | Client can't show proper error UI |
| **200 for unauthorized** | Security risk | Unauthorized access not logged |
