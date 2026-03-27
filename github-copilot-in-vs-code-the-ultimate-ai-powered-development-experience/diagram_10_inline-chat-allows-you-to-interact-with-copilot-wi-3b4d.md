# diagram_10_inline-chat-allows-you-to-interact-with-copilot-wi-3b4d


```mermaid
sequenceDiagram
    participant User
    participant Editor
    participant Inline
    
    User->>Editor: Highlight code
    User->>Editor: Ctrl/Cmd + I
    Editor->>Inline: Opens input box at cursor
    User->>Inline: Types request
    Inline->>Copilot: Sends request with context
    Copilot-->>Inline: Returns response
    Inline-->>User: Shows diff preview
    User->>Inline: Accept or modify
```
