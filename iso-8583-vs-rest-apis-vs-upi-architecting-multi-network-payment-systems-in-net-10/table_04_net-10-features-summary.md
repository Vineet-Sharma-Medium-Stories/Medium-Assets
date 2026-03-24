# table_04_net-10-features-summary


| Feature | Prior .NET Version | .NET 10 Enhancement | Impact |
|---------|-------------------|---------------------|---------|
| **JSON Serialization** | Reflection-based with runtime type discovery | Source-generated with compile-time contracts | 30-50% reduction in allocations, improved startup time |
| **OpenAPI** | Third-party Swashbuckle | Native Microsoft.AspNetCore.OpenApi | Reduced dependencies, better integration |
| **Slim Builder** | WebApplication.CreateBuilder | WebApplication.CreateSlimBuilder | ~40% memory reduction for API-only services |
| **Rate Limiting** | Custom implementation or external | Built-in TokenBucket/Concurrency algorithms | Production-grade rate limiting with minimal code |
| **Regex** | Runtime compilation | Source-generated with \[GeneratedRegex\] | Compile-time validation, no runtime overhead |
| **Distributed Tracing** | ActivitySource with manual propagation | Enhanced context propagation, OTLP exporter | Better observability in cloud-native environments |
| **Health Checks** | Basic implementation | Database probes, custom check templates | Comprehensive health reporting for orchestration |
| **Native AOT** | Framework-dependent deployment | PublishAot for self-contained binaries | Sub-100ms startup, minimal container size |
| **Hybrid Cache** | IMemoryCache only | Tiered cache with IMemoryCache + IDistributedCache | Optimal performance with fallback |
| **ActivitySource** | Manual tag management | Pre-allocated tag lists, reduced allocations | Lower overhead for high-throughput tracing |
