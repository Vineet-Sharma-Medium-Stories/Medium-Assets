# diagram_19_3-start-safe--use-rm--i-interactive-for-d39b


```mermaid
flowchart TD
    Command[Dangerous Command Detected]
    Check{Confirm?}
    Safe[Safe Command Suggestion]
    Execute[Execute with confirmation]
    
    Command --> Check
    Check -->|Yes| Execute
    Check -->|No| Safe
```
