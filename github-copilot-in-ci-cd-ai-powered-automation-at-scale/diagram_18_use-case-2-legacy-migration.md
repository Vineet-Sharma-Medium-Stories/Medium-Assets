# diagram_18_use-case-2-legacy-migration


```mermaid
flowchart LR
    Legacy[Legacy Monolith]
    Copilot[AI Analyzes]
    Plan[Migration Plan]
    Steps[Automated Steps:<br/>1. Extract service<br/>2. Create pipeline<br/>3. Deploy independently]
    
    Legacy --> Copilot --> Plan --> Steps
```
