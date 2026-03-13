# Table 6: Lifetime Comparison:

| Lifetime | Instance Per | Created When | Use Case |
|----------|--------------|--------------|----------|
| Transient | Every request | Every time | Stateless services |
| Scoped | Request/Scope | Once per scope | Database contexts |
| Singleton | Application | First request | Caches, configuration |
