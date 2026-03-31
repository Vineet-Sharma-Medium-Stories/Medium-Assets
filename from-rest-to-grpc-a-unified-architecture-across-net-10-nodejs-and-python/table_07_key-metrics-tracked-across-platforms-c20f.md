# ### Key Metrics Tracked Across Platforms

| Metric | .NET 10 | Node.js | Python | Description |
|--------|---------|---------|--------|-------------|
| `grpc.server.duration` | ✅ | ✅ | ✅ | Request latency histogram |
| `grpc.server.requests` | ✅ | ✅ | ✅ | Total request counter |
| `grpc.server.streams` | ✅ | ✅ | ✅ | Active streams gauge |
| `grpc.server.sent_bytes` | ✅ | ✅ | ✅ | Response size histogram |
| `custom.telemetry.rate` | ✅ | ✅ | ✅ | Telemetry updates per second |
| `custom.command.latency` | ✅ | ✅ | ✅ | Command delivery latency |
| `custom.active_vehicles` | ✅ | ✅ | ✅ | Active vehicles gauge |
| `custom.ml.inference_time` | ❌ | ❌ | ✅ | ML inference duration |
