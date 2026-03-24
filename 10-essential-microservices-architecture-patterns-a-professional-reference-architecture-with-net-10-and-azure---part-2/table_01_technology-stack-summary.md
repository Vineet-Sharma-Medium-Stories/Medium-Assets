# table_01_technology-stack-summary


| Component | Technology | Justification |
|-----------|------------|---------------|
| **Runtime** | .NET 10 | Native AOT, minimal APIs, enhanced performance, improved memory management |
| **ORM** | EF Core 10 | Compiled models, bulk updates, query splitting, JSON columns support |
| **API Gateway** | YARP + Azure APIM | Flexibility of custom code + managed service benefits with enterprise features |
| **Service Mesh** | Dapr on ACA | Language-agnostic, built-in patterns, mTLS, observability without code changes |
| **Secrets** | Azure Key Vault | HSM-backed, managed identities, automatic rotation, audit logging |
| **Database** | Azure SQL + Cosmos DB | Polyglot persistence per service need with optimal performance characteristics |
| **Messaging** | Azure Service Bus | Enterprise-grade, sessions, dead-lettering, duplicate detection, partitioning |
| **Container** | Docker + ACR | Secure, private registry with vulnerability scanning, geo-replication |
| **Monitoring** | Application Insights | Distributed tracing, metric collection, log analytics, smart detection |
| **Compute** | Container Apps + Functions | Flexible scaling options based on workload characteristics |
