# table_10_real-world-consequences


| Violation | Impact | Real Example from the Incident |
|-----------|--------|--------------------------------|
| **Stack trace exposure** | Security vulnerability | Database connection string in error response |
| **Internal error codes** | Confusing API consumers | SQL error code returned to frontend |
| **No error correlation** | Difficult debugging | Cannot trace error to specific request |
| **Inconsistent error format** | Poor client experience | Each endpoint returned different error structure |
