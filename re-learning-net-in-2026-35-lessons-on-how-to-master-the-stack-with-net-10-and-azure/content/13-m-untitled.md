# Mermaid Diagram 13: Untitled

```mermaid
graph TD
    subgraph "Azure Monitoring Stack"
        App[.NET 10 App] --> SDK[Application Insights SDK]
        
        subgraph "Telemetry"
            SDK --> Requests[Requests]
            SDK --> Dependencies[Dependencies]
            SDK --> Exceptions[Exceptions]
            SDK --> Traces[Traces]
            SDK --> Metrics[Metrics]
        end
        
        Requests --> AI[Application Insights]
        Dependencies --> AI
        Exceptions --> AI
        Traces --> AI
        Metrics --> AI
        
        AI --> LA[Log Analytics Workspace]
        
        subgraph "Alerting"
            LA --> Alerts[Alert Rules]
            Alerts --> ActionGroups[Action Groups]
            ActionGroups --> Email[Email]
            ActionGroups --> SMS[SMS]
            ActionGroups --> Webhook[Webhook]
            ActionGroups --> Runbook[Automation Runbook]
        end
        
        subgraph "Dashboards"
            LA --> Workbooks[Azure Workbooks]
            LA --> Dashboards[Shared Dashboards]
            LA --> PowerBI[Power BI]
        end
        
        subgraph "Auto-Remediation"
            Runbook --> Restart[Restart Service]
            Runbook --> Scale[Scale Out]
            Runbook --> Failover[Failover to DR]
        end
    end
```
