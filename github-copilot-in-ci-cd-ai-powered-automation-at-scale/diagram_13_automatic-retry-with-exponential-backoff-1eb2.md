# diagram_13_automatic-retry-with-exponential-backoff-1eb2


```mermaid
flowchart LR
    Step[Job Step]
    Fail{Fails?}
    Retry[Retry with backoff]
    Success[Success]
    Notify[Notify on failure]
    
    Step --> Fail
    Fail -->|No| Success
    Fail -->|Yes| Retry
    Retry -->|Succeeded| Success
    Retry -->|Max retries| Notify
```
