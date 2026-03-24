# diagram_01_before-diving-into-the-concepts-lets-visualize-t-99f6


```mermaid
---
config:
  theme: base
  layout: elk
---
graph LR
    subgraph Basic["Basic SQL Mindset"]
        A["Reactive<br/>Wait for Questions"] --> B["Linear Queries<br/>One-off Solutions"]
        B --> C["Hardcoded Logic<br/>Brittle Code"]
        C --> D["Raw Data Output<br/>No Context"]
        D --> E["Personal Scripts<br/>Knowledge Silo"]
    end
    
    subgraph Advanced["Advanced SQL Mindset"]
        F["Proactive<br/>Anticipate Needs"] --> G["Modular CTEs<br/>Reusable Components"]
        G --> H["Config-Driven<br/>Maintainable"]
        H --> I["Business Insights<br/>Strategic Value"]
        I --> J["Team Assets<br/>Force Multiplier"]
    end
    
    Basic -->|Promotion Path| Advanced
    
    style Basic fill:#ffcccc,stroke:#cc0000,stroke-width:2px
    style Advanced fill:#ccffcc,stroke:#00aa00,stroke-width:2px
```
