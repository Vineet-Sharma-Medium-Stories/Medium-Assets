# Table 8: Secret Management Comparison Table

| Feature | AWS Secrets Manager | Parameter Store | Vault | Sealed Secrets | External Secrets |
|---------|--------------------|-----------------|--------|----------------|------------------|
| **Secret rotation** | ✅ Automatic | ❌ Manual | ✅ Dynamic | ❌ Manual | ❌ Manual |
| **Audit logging** | ✅ CloudTrail | ✅ CloudTrail | ✅ Detailed | ❌ | ✅ CloudTrail |
| **IAM integration** | ✅ Native | ✅ Native | ⚠️ Via auth | ❌ | ✅ Native |
| **Multi-cloud** | ❌ AWS only | ❌ AWS only | ✅ Yes | ✅ Yes | ⚠️ AWS focused |
| **GitOps friendly** | ❌ | ❌ | ❌ | ✅ | ⚠️ Needs controller |
| **Cost** | 💰 $0.40/secret/month | 🆓 Free | 💰 Self-managed | 🆓 Free | 🆓 Free |
| **Complexity** | Low | Low | High | Medium | Medium |
