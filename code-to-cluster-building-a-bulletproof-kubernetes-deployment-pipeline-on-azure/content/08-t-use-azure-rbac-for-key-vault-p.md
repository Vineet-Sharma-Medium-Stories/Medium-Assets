# Table 8: Use Azure RBAC for Key Vault (preview)

| Feature | Azure Key Vault | Parameter Store (App Config) | Vault | Sealed Secrets | External Secrets |
|---------|-----------------|------------------------------|--------|----------------|------------------|
| **Secret rotation** | ⚠️ Manual/API | ❌ Manual | ✅ Dynamic | ❌ Manual | ❌ Manual |
| **Audit logging** | ✅ Log Analytics | ✅ Log Analytics | ✅ Detailed | ❌ | ✅ Log Analytics |
| **Azure AD integration** | ✅ Native | ✅ Native | ⚠️ Via auth | ❌ | ✅ Via AAD |
| **Multi-cloud** | ❌ Azure only | ❌ Azure only | ✅ Yes | ✅ Yes | ⚠️ Azure supported |
| **GitOps friendly** | ❌ | ❌ | ❌ | ✅ | ⚠️ Needs controller |
| **Cost** | 💰 Free tier available | 💰 Free tier | 💰 Self-managed | 🆓 Free | 🆓 Free |
| **Complexity** | Low | Low | High | Medium | Medium |
