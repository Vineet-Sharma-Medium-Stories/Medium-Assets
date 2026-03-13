# Table 10: Azure Resilience Patterns:

| Pattern | Azure Service | Use Case |
|---------|--------------|----------|
| Retry | Polly + Azure SDKs | Transient failures |
| Circuit Breaker | Polly | Preventing cascading failures |
| Queue-based load leveling | Service Bus | Smooth out traffic spikes |
| Competing Consumers | Service Bus | Scale out processing |
| Saga Pattern | Durable Functions | Distributed transactions |
| Health Endpoint | App Service | Load balancer checks |
| Throttling | API Management | Rate limiting |
| Fallback | Redis Cache | Return cached data |
