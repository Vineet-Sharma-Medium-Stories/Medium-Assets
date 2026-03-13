# Table 4: Database credentials

| Service | Best For | Key Features |
|---------|----------|--------------|
| **AWS Secrets Manager** | AWS-native apps | Automatic rotation, fine-grained IAM, cross-region replication |
| **AWS Systems Manager Parameter Store** | Simple config | Hierarchical storage, free tier, plaintext or encrypted |
| **HashiCorp Vault** | Multi-cloud, dynamic secrets | Unified secrets, encryption as a service, leasing |
| **Kubernetes Secrets + SOPS** | GitOps workflows | Encrypted secrets in Git, decrypted at deploy time |
| **External Secrets Operator** | Bridge between AWS and K8s | Syncs AWS Secrets Manager/Parameter Store to K8s Secrets |
| **Sealed Secrets** | GitOps with encryption | Encrypt secrets for safe storage in Git |
