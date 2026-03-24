# diagram_07_without-governance-different-departments-define-m-5afd


```mermaid
---
config:
  theme: base
  layout: elk
---
graph TD
    subgraph Basic [No Governance: Chaos]
        direction TB
        B1[Marketing: "Active" = last 30 days] --> B5[Inconsistent Reports]
        B2[Sales: "Active" = last 90 days] --> B5
        B3[Anyone can access<br/>any data] --> B6[Security Risk]
        B4[No audit trail<br/>No compliance] --> B7[Regulatory Exposure]
    end
    
    subgraph Advanced [Data Governance: Controlled]
        direction TB
        G1[Business Glossary<br/>Single definitions] --> G5[Consistent Metrics]
        G2[Row-Level Security<br/>Access controls] --> G6[Data Protected]
        G3[Audit Logs<br/>Compliance tracking] --> G7[Regulatory Ready]
        G4[Data Lineage<br/>Traceability] --> G8[Full Visibility]
    end
    
    style Basic fill:#ffcccc,stroke:#cc0000,stroke-width:2px
    style Advanced fill:#ccffcc,stroke:#00aa00,stroke-width:2px
```
