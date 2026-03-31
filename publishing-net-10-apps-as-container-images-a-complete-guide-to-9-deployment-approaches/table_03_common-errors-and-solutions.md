# **Common Errors and Solutions**:

| Error | Likely Cause | Solution |
|-------|--------------|----------|
| `Access denied` | Missing ACR permissions | `az role assignment add --assignee <SP> --role AcrPush --scope <acr-id>` |
| `Container registry not found` | Invalid registry name | Verify with `az acr list --query "[].loginServer"` |
| `Image tag invalid` | Tag format issue | Use lowercase alphanumeric, dots, hyphens only |
| `Build failed: MSB4018` | SDK bug or missing target | Update to latest .NET 10 SDK, try `dotnet workload update` |
