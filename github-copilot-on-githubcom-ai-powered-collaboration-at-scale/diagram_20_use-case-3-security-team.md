# diagram_20_use-case-3-security-team


```mermaid
flowchart LR
    Vuln[Security vulnerability reported]
    Search[AI searches codebase for similar patterns]
    List[Lists all affected locations]
    Fixes[Suggests fixes for each location]
    Review[Generates security review summary]
    
    Vuln --> Search --> List --> Fixes --> Review
```
