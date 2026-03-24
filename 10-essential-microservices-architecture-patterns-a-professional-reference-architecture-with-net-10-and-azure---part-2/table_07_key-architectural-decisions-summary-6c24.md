# table_07_key-architectural-decisions-summary-6c24


| Pattern | Azure Service | Key Benefits |
|---------|--------------|--------------|
| API Gateway | Azure API Management + YARP | Centralized security, routing, rate limiting |
| Service Discovery | Azure Container Apps DNS | Dynamic service location without hardcoding |
| Load Balancing | Azure Front Door + Container Apps | Global distribution, auto-scaling |
| Circuit Breaker | Polly + Application Insights | Resilience, failure isolation |
| Event-Driven | Azure Service Bus | Decoupled communication, scalability |
| CQRS | Azure SQL + Cosmos DB | Optimized read/write performance |
| Saga | Dapr + Cosmos DB | Distributed transaction management |
| Service Mesh | Dapr on ACA | Zero-code infrastructure features |
| Distributed Tracing | Application Insights + OpenTelemetry | End-to-end visibility |
| Containerization | ACR + ACA | Consistent deployment, portability |
