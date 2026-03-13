# Table 4: Isolation Levels Explained:

| Level | Dirty Read | Non-Repeatable Read | Phantom Read | Concurrency |
|-------|------------|---------------------|--------------|-------------|
| Read Uncommitted | ✅ | ✅ | ✅ | Highest |
| Read Committed | ❌ | ✅ | ✅ | High |
| Repeatable Read | ❌ | ❌ | ✅ | Medium |
| Serializable | ❌ | ❌ | ❌ | Lowest |
