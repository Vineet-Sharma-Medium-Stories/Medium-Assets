# diagram_03_technical-deep-dive-we-use-keycloak-as-our-id-a841


```mermaid
sequenceDiagram
    participant User as Sarah (AI Developer)
    portal Browser as Developer Portal
    participant Keycloak as Keycloak IdP
    participant AD as Active Directory
    participant API as API Gateway
    
    User->>Browser: Clicks "Login with SSO"
    Browser->>Keycloak: Redirect to /auth
    Keycloak->>AD: Validate credentials
    AD-->>Keycloak: Authentication success
    Keycloak-->>Browser: Authorization code
    Browser->>Keycloak: Exchange code for tokens
    Keycloak-->>Browser: Access Token (JWT) + Refresh Token
    Browser->>API: API Request + Bearer Token
    API->>API: Validate JWT signature
    API->>API: Check expiry & permissions
    API-->>Browser: Response
```
