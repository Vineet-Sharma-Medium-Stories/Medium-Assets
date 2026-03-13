# Table 1: Phase 2: The CI Pipeline (The Build)

| Tool | Type | Pros | Cons |
|------|------|------|------|
| **Jenkins** | Self-hosted (EC2/EKS) | Highly customizable, huge plugin ecosystem | Maintenance overhead, requires infrastructure management |
| **GitHub Actions** | SaaS + Self-hosted runners | Native Git integration, free for public repos, matrix builds | Limited customization for complex pipelines |
| **AWS CodePipeline + CodeBuild** | Fully managed AWS | Deep AWS integration, no servers to manage | AWS-specific, learning curve for non-AWS users |
| **GitLab CI** | SaaS or Self-hosted | Single application for SCM and CI/CD | Requires GitLab for full experience |
| **CircleCI** | SaaS | Fast, easy to configure, good Docker support | Can get expensive at scale |
