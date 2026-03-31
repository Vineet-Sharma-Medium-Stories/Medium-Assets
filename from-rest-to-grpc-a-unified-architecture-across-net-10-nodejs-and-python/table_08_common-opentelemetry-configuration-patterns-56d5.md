# ### Common OpenTelemetry Configuration Patterns

| Platform | Trace Exporter | Metrics Exporter | Log Integration |
|----------|----------------|------------------|-----------------|
| **.NET 10** | OTLP/gRPC via `AddOtlpExporter()` | Prometheus via `AddPrometheusExporter()` | `ILogger` with OpenTelemetry |
| **Node.js** | OTLP/gRPC via `OTLPTraceExporter` | Prometheus via `@opentelemetry/exporter-prometheus` | `pino` with OpenTelemetry |
| **Python** | OTLP/gRPC via `OTLPSpanExporter` | Prometheus via `PrometheusMetricReader` | `structlog` with OpenTelemetry |
