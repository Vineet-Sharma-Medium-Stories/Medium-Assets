# Mermaid Diagram 1: And this is just the beginning.

```mermaid
graph TD
    A[Developer: git commit] --> B{Code Repository<br/>GitHub / CodeCommit / GitLab};
    B --> C[CI/CD Trigger<br/>Jenkins / GitHub Actions / CodePipeline];

    subgraph CI Pipeline [Continuous Integration - AWS Build]
        direction LR
        C --> D[Build & Test];
        D --> E[Security Scan<br/>Trivy / Snyk / SonarQube];
        E --> F[Build Docker Image];
        F --> G[Push to Registry];
    end

    G --> H[(Container Registry<br/>ECR / Docker Hub / Harbor)];

    subgraph Secret Management [Secret Management Layer]
        I[(Secrets Manager<br/>AWS Secrets Manager / HashiCorp Vault)]
        J[(Service Account<br/>IRSA / K8s Secrets)]
        I --> J
    end

    subgraph CD Pipeline [Continuous Deployment - K8s]
        H --> K[Update Deployment Manifest];
        J --> L[Inject Secrets at Runtime];
        K --> L;
        L --> M{Deploy to Cluster};
    end

    M --> N[STAGING Environment];
    M --> O[PRODUCTION Environment];

    subgraph Observability [Monitoring & Observability]
        N --> P[Prometheus / Grafana<br/>Datadog / New Relic];
        O --> P;
        P --> Q[Alerting / Rollback];
    end

    Q -- "Rollback Trigger" --> K;
```
