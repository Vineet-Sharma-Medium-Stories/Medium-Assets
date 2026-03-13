# Table 4: Database credentials

| Service | Best For | Key Features |
|---------|----------|--------------|
| **Azure Key Vault** | Azure-native apps | Automatic rotation, fine-grained access policies, HSM support |
| **HashiCorp Vault** | Multi-cloud, dynamic secrets | Unified secrets, encryption as a service, leasing |
| **Kubernetes Secrets + SOPS** | GitOps workflows | Encrypted secrets in Git, decrypted at deploy time |
| **External Secrets Operator** | Bridge between Azure and K8s | Syncs Azure Key Vault to K8s Secrets |
| **Sealed Secrets** | GitOps with encryption | Encrypt secrets for safe storage in Git |
| **Azure App Configuration** | Feature flags + config | Centralized configuration management |
