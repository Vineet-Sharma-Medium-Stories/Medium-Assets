# table_09_real-world-consequences


| Violation | Impact | Real Example from the Incident |
|-----------|--------|--------------------------------|
| **Missing null checks** | NullReferenceException in production | Optional field omitted caused crash |
| **No length limits** | Database overflow, DoS attacks | 10MB string stored, filling transaction log |
| **No format validation** | Data inconsistency | Email addresses with invalid format stored |
| **No business validation** | Invalid business states | Order with negative quantity processed |
| **No SQL parameterization** | SQL injection | Direct string concatenation in queries |
