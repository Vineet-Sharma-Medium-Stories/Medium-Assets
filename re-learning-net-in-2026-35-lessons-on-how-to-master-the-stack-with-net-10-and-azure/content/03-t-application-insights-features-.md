# Table 3: Application Insights Features in Azure:

| Feature | What It Tells You | Kusto Query Example |
|---------|-------------------|---------------------|
| Requests | HTTP traffic, response times | `requests \| where success == false` |
| Dependencies | Calls to databases, APIs | `dependencies \| where target contains "sql"` |
| Exceptions | Thrown exceptions with stack traces | `exceptions \| where problemId contains "NullReference"` |
| Traces | Log messages | `traces \| where message contains "payment"` |
| Metrics | Custom performance metrics | `customMetrics \| where name == "PaymentProcessingTime"` |
| Availability | Uptime monitoring | `availabilityResults \| where success == false` |
