# Table 1: Phase 2: The CI Pipeline (The Build)

| Tool | Type | Pros | Cons |
|------|------|------|------|
| **Jenkins** | Self-hosted (Azure VMs/AKS) | Highly customizable, huge plugin ecosystem | Maintenance overhead, requires infrastructure management |
| **GitHub Actions** | SaaS + Self-hosted runners | Native Git integration, free for public repos, matrix builds | Limited customization for complex pipelines |
| **Azure Pipelines** | Fully managed Azure DevOps | Deep Azure integration, unlimited minutes for open source, YAML or GUI | Learning curve, can get expensive for private projects |
| **GitLab CI** | SaaS or Self-hosted | Single application for SCM and CI/CD | Requires GitLab for full experience |
| **CircleCI** | SaaS | Fast, easy to configure, good Docker support | Can get expensive at scale |
