# table_03_saga-implementation-options


| Pattern | Coordination | Complexity | Visibility | Best For |
|---------|--------------|------------|------------|----------|
| **Choreography** | Decentralized (events) | Lower | Less visibility | Simple workflows with few services |
| **Orchestration** | Centralized orchestrator | Higher | Full visibility | Complex workflows, business processes |
| **State Machine** | Durable Functions | Medium | Good visibility | Long-running processes with human steps |
