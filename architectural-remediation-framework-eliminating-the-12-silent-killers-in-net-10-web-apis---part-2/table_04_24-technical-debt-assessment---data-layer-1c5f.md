# table_04_24-technical-debt-assessment---data-layer-1c5f


| Component | Anti-Patterns | Debt Days | Priority | Business Impact |
|-----------|---------------|-----------|----------|-----------------|
| **Order Queries** | No Pagination, Over-fetching | 8 days | Critical | 50MB responses, 3-second queries |
| **API Responses** | EF Entities Exposed | 4 days | Critical | Security vulnerabilities, circular references |
| **Error Handling** | Wrong Status Codes | 2 days | High | Frontend can't distinguish errors |
| **Total Data Layer Debt** | | **14 days** | | $30,000+ in cloud costs/month |
