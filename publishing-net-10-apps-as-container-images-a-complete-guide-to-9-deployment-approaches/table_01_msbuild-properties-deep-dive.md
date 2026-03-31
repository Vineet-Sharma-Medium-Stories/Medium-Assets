# ### MSBuild Properties Deep Dive

| Property | Description | Example |
|----------|-------------|---------|
| `ContainerBaseImage` | Customize base image | `mcr.microsoft.com/dotnet/runtime-deps:10.0` |
| `ContainerImageTags` | Multiple tags | `1.0.0;latest;$(Build.BuildNumber)` |
| `ContainerWorkingDirectory` | Container startup path | `/app` |
| `ContainerPort` | Exposed ports | `8080;443` |
| `ContainerEnvironmentVariables` | Embedded env vars | `ASPNETCORE_ENVIRONMENT=Production` |
| `ContainerRegistry` | Target registry | `myregistry.azurecr.io` |
| `ContainerRepository` | Image repository | `myapp` |
| `ContainerArchiveOutputPath` | Tarball export path | `./output/image.tar.gz` |
| `ContainerLabel` | OCI image labels | `org.opencontainers.image.version=1.0.0` |
| `ContainerUser` | Container user | `appuser` |
| `ContainerEntrypoint` | Override entrypoint | `dotnet myapp.dll --mode=production` |
