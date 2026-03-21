# table_05_33-technology-stack---part-1-focus-094d


| Layer | Technology | Version | Purpose | Anti-Pattern Addressed |
|-------|------------|---------|---------|------------------------|
| **Mediation** | MediatR | 12.0 | Decouple HTTP from business logic | #1 Fat Controllers |
| **Validation** | FluentValidation | 11.0 | Declarative validation rules | #2 No Validation |
| **Error Handling** | Problem Details | Built-in | RFC 7807 compliant errors | #3 Raw Exceptions |
| **Async** | .NET 10 Native | 10.0 | Async/await patterns | #4 Blocking Async |
| **Cancellation** | CancellationToken | Built-in | Resource cleanup | #5 No Cancellation |
| **Logging** | Serilog | 4.0 | Structured logging | #11 No Observability |
| **Tracing** | OpenTelemetry | 1.7 | Distributed tracing | #11 No Observability |
| **Metrics** | OpenTelemetry | 1.7 | Business & system metrics | #11 No Observability |
