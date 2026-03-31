# Publishing .NET 10 Apps as Container Images: A Complete Guide to 9 Deployment Approaches
## The .NET 10 series explores the full spectrum of container deployment options for modern .NET applications, from SDK-native simplicity to Kubernetes orchestration with Azure

![alt text](https://raw.githubusercontent.com/Vineet-Sharma-Medium-Stories/Medium-Build-Tool/refs/heads/main/Publishing%20.NET%2010%20Apps%20as%20Container%20Images/publishing-net-10-apps-as-container-images-a-complete-guide-to-9-deployment-approaches/images/Publishing-.NET-10-Apps-as-Container-Images---A-Complete-Guide-to-9-Deployment-Approaches.png)

## Introduction: The Evolution of .NET Container Publishing

The containerization landscape for .NET applications has undergone a remarkable transformation. When Docker first emerged in 2013, .NET developers were confined to Windows containers, a niche approach that felt like an afterthought. Fast forward to 2026, and .NET 10 represents the culmination of a decade-long journey toward seamless containerization, offering developers an unprecedented array of deployment options that rival—and in some ways surpass—the ecosystem maturity of languages like Go and Rust.

What makes .NET 10 particularly special is the native container support baked directly into the SDK. Gone are the days when writing a Dockerfile was mandatory. Today, a single `dotnet publish` command can produce production-ready OCI images, push them directly to Azure Container Registry, or even export them as tarballs for security scanning—all without Docker installed on your machine.

This shift reflects a broader industry trend: the decoupling of container image creation from container runtimes. As organizations embrace Podman's daemonless architecture for enhanced security, and as air-gapped environments demand greater control over the supply chain, the .NET SDK's container tooling provides the flexibility to adapt to any infrastructure requirement.

```mermaid
```

![Introduction: The Evolution of .NET Container Publishing](https://raw.githubusercontent.com/Vineet-Sharma-Medium-Stories/Medium-Build-Tool/refs/heads/main/Publishing%20.NET%2010%20Apps%20as%20Container%20Images/publishing-net-10-apps-as-container-images-a-complete-guide-to-9-deployment-approaches/images/diagram_01_this-shift-reflects-a-broader-industry-trend-the-97a0.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/publishing-net-10-apps-as-container-images-a-complete-guide-to-9-deployment-approaches/diagram_01_this-shift-reflects-a-broader-industry-trend-the-97a0.md)


---

### Stories at a Glance

**Companion stories in this series:**

- 📚 **1. .NET SDK Native Container Publishing Deep Dive: The Complete Reference** – Comprehensive coverage of MSBuild properties, Native AOT optimization, CI/CD pipeline patterns, performance benchmarks, and troubleshooting guides

- 🚀 **2. .NET SDK Native Container Publishing: Building OCI Images Without Docker** – A deep dive into MSBuild configuration, multi-architecture builds, Native AOT optimization, and direct Azure Container Registry integration with workload identity federation

- 🐳 **3. Traditional Dockerfile with Docker: The Classic Approach** – Mastering multi-stage builds, build cache optimization, .dockerignore patterns, and Azure Container Registry authentication for enterprise CI/CD pipelines

- 🔐 **4. Traditional Dockerfile with Podman: The Daemonless Alternative** – Transitioning from Docker to Podman, rootless containers for enhanced security, podman-compose workflows, and Azure ACR integration with Podman Desktop

- ⚡ **5. Azure Developer CLI (azd) with .NET Aspire: The Turnkey Solution** – Full-stack deployments with `azd up`, Azure Container Apps provisioning, Redis caching, and infrastructure-as-code with Bicep templates

- 🖱️ **6. Visual Studio 2026 GUI Publishing: Drag-and-Drop Azure Deployments** – Leveraging Visual Studio's built-in Podman/Docker support, one-click publish to Azure Container Registry, and debugging containerized apps with Hot Reload

- 🔒 **7. Tarball Export + Runtime Load: Security-First CI/CD Workflows** – Generating container tarballs without a runtime, integrating with Trivy/Grype for vulnerability scanning, and deploying to air-gapped Azure environments

- 🔄 **8. Podman with .NET SDK Native Publishing: Hybrid Workflows** – Combining SDK-native builds with Podman for local testing, multi-architecture emulation, and Azure Container Registry push strategies

- 🛠️ **9. konet: Multi-Platform Container Builds Without Docker** – Using the konet .NET tool for cross-platform image generation, ARM64/AMD64 simultaneous builds, and GitHub Actions optimization

---

## 1. 📚 .NET SDK Native Container Publishing Deep Dive: The Complete Reference

This comprehensive reference covers the SDK-native approach with exhaustive detail on every configuration option, optimization technique, and troubleshooting pattern. As the foundational method for modern .NET containerization, mastering this approach unlocks the full potential of .NET 10's container capabilities.

### MSBuild Properties Deep Dive

![9. konet: Multi-Platform Container Builds Without Docker](https://raw.githubusercontent.com/Vineet-Sharma-Medium-Stories/Medium-Build-Tool/refs/heads/main/Publishing%20.NET%2010%20Apps%20as%20Container%20Images/publishing-net-10-apps-as-container-images-a-complete-guide-to-9-deployment-approaches/images/table_01_msbuild-properties-deep-dive.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/publishing-net-10-apps-as-container-images-a-complete-guide-to-9-deployment-approaches/table_01_msbuild-properties-deep-dive.md)


### Native AOT Container Optimization

Native AOT (Ahead-of-Time) compilation transforms .NET applications into self-contained native executables with dramatic improvements:

**Configuration**:
```xml
<PropertyGroup>
  <PublishAot>true</PublishAot>
  <ContainerBaseImage>mcr.microsoft.com/dotnet/runtime-deps:10.0</ContainerBaseImage>
  <ContainerImageTags>aot-latest;$(Version)</ContainerImageTags>
</PropertyGroup>
```

**Results**:
- Image size: 200MB → 10-15MB (90%+ reduction)
- Startup time: 100-200ms → 1-2ms (99% reduction)
- Memory footprint: 50-100MB → 10-20MB
- Disk I/O: Minimal, as everything is pre-compiled

**Limitations**:
- No dynamic code generation (Reflection.Emit)
- Limited support for runtime code generation
- Requires trimming-compatible libraries
- Some reflection scenarios require explicit annotations

**Optimization Tips**:
```xml
<PropertyGroup>
  <!-- Enable all trimming optimizations -->
  <TrimMode>full</TrimMode>
  
  <!-- Disable debug symbols for smaller images -->
  <DebugType>none</DebugType>
  
  <!-- Optimize for size rather than speed -->
  <OptimizationPreference>Size</OptimizationPreference>
  
  <!-- Remove invariant globalization for smaller footprint -->
  <InvariantGlobalization>true</InvariantGlobalization>
</PropertyGroup>
```

### CI/CD Pipeline Integration

**GitHub Actions with Workload Identity**:
```yaml
name: Build and Deploy
on:
  push:
    branches: [main]

permissions:
  id-token: write
  contents: read

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    
    - name: Setup .NET
      uses: actions/setup-dotnet@v4
      with:
        dotnet-version: '10.0.x'
    
    - name: Azure Login
      uses: azure/login@v1
      with:
        client-id: ${{ secrets.AZURE_CLIENT_ID }}
        tenant-id: ${{ secrets.AZURE_TENANT_ID }}
        subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
    
    - name: Build and push container
      run: |
        az acr login --name ${{ secrets.ACR_NAME }}
        dotnet publish /t:PublishContainer \
          -p ContainerRegistry=${{ secrets.ACR_NAME }}.azurecr.io \
          -p ContainerRepository=myapp \
          -p ContainerImageTags="${{ github.sha }};latest" \
          -p ContainerImageTags=$(date +'%Y%m%d')
```

**Azure DevOps Pipeline**:
```yaml
trigger:
- main

variables:
  acrName: 'myregistry'
  imageRepository: 'myapp'
  tag: '$(Build.BuildId)'

stages:
- stage: Build
  jobs:
  - job: BuildAndPush
    pool:
      vmImage: 'ubuntu-latest'
    steps:
    - task: UseDotNet@2
      inputs:
        version: '10.0.x'
    
    - task: AzureCLI@2
      displayName: 'ACR Login'
      inputs:
        azureSubscription: 'service-connection'
        scriptType: 'bash'
        scriptLocation: 'inlineScript'
        inlineScript: |
          az acr login --name $(acrName)
    
    - task: DotNetCoreCLI@2
      displayName: 'Build and Push Container'
      inputs:
        command: 'publish'
        arguments: '/t:PublishContainer
          -p ContainerRegistry=$(acrName).azurecr.io
          -p ContainerRepository=$(imageRepository)
          -p ContainerImageTags=$(tag);latest'
```

**Workload Identity Federation Setup**:
```bash
# Create federated credential for GitHub Actions
az ad app federated-credential create \
  --id <application-id> \
  --parameters credential.json

# credential.json
{
  "name": "github-actions",
  "issuer": "https://token.actions.githubusercontent.com",
  "subject": "repo:myorg/myrepo:ref:refs/heads/main",
  "description": "GitHub Actions federation",
  "audiences": ["api://AzureADTokenExchange"]
}
```

### Performance Benchmarking

![Table](https://raw.githubusercontent.com/Vineet-Sharma-Medium-Stories/Medium-Build-Tool/refs/heads/main/Publishing%20.NET%2010%20Apps%20as%20Container%20Images/publishing-net-10-apps-as-container-images-a-complete-guide-to-9-deployment-approaches/images/table_02_performance-benchmarking.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/publishing-net-10-apps-as-container-images-a-complete-guide-to-9-deployment-approaches/table_02_performance-benchmarking.md)


### Layer Structure Analysis

```mermaid
```

![Diagram](https://raw.githubusercontent.com/Vineet-Sharma-Medium-Stories/Medium-Build-Tool/refs/heads/main/Publishing%20.NET%2010%20Apps%20as%20Container%20Images/publishing-net-10-apps-as-container-images-a-complete-guide-to-9-deployment-approaches/images/diagram_02_layer-structure-analysis.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/publishing-net-10-apps-as-container-images-a-complete-guide-to-9-deployment-approaches/diagram_02_layer-structure-analysis.md)


### Troubleshooting Guide

**Authentication Errors**:
```bash
# Ensure Azure CLI is logged in
az login
az acr login --name myregistry

# Verify token
az acr show --name myregistry --query loginServer

# For CI/CD, use service principal
az acr login --name myregistry --username $SP_APP_ID --password $SP_PASSWORD
```

**Layer Caching Issues**:
```bash
# Clean build to verify
dotnet clean
rm -rf obj bin
dotnet publish /t:PublishContainer --no-cache

# Inspect layers
podman history myapp:latest
```

**Base Image Compatibility**:
```bash
# Test base image locally first
podman run -it mcr.microsoft.com/dotnet/aspnet:10.0 /bin/bash

# Verify architecture compatibility
podman inspect mcr.microsoft.com/dotnet/aspnet:10.0 | grep Architecture
```

**Common Errors and Solutions**:

![Common Errors and Solutions](https://raw.githubusercontent.com/Vineet-Sharma-Medium-Stories/Medium-Build-Tool/refs/heads/main/Publishing%20.NET%2010%20Apps%20as%20Container%20Images/publishing-net-10-apps-as-container-images-a-complete-guide-to-9-deployment-approaches/images/table_03_common-errors-and-solutions.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/publishing-net-10-apps-as-container-images-a-complete-guide-to-9-deployment-approaches/table_03_common-errors-and-solutions.md)


### Security Best Practices

**Non-Root User**:
```xml
<PropertyGroup>
  <ContainerUser>appuser</ContainerUser>
</PropertyGroup>
```

**SBOM Generation**:
```bash
# Generate SBOM during publish
dotnet publish /t:PublishContainer \
  -p GenerateSBOM=true \
  -p SBOMOutputPath=./sbom
```

**Vulnerability Scanning**:
```bash
# Scan image after build
trivy image myregistry.azurecr.io/myapp:latest

# Or scan tarball before push
trivy image --input ./output/myapp.tar.gz
```

**Image Signing**:
```bash
# Sign image with Cosign
cosign sign --key cosign.key myregistry.azurecr.io/myapp:latest

# Verify signature
cosign verify --key cosign.pub myregistry.azurecr.io/myapp:latest
```

### Use Cases

This comprehensive reference is essential for:
- Platform engineering teams standardizing on SDK-native
- Developers optimizing for specific deployment targets (ACA, AKS, ACI)
- Teams migrating from Dockerfile-based workflows
- Anyone seeking to master .NET 10 containerization

---

## 2. 🚀 .NET SDK Native Container Publishing: Building OCI Images Without Docker

At the heart of modern .NET containerization lies the SDK's native container publishing capability, introduced in .NET 8 and refined dramatically through .NET 10. This approach fundamentally reimagines how .NET applications become containers, treating container images as first-class build artifacts rather than afterthoughts assembled via external tooling.

### How It Works

When you execute `dotnet publish` with the `PublishContainer` target, the .NET SDK orchestrates a sophisticated process:

1. **Dependency Analysis**: The SDK examines your project file, identifying frameworks, runtime identifiers, and platform targets. For .NET 10, this includes support for Native AOT scenarios where the application is compiled directly to machine code.

2. **Application Publishing**: Your application is published to a staging directory using the standard publish process, with optimizations for container environments—including trimming, ready-to-run compilation, and assembly linking.

3. **Base Image Selection**: By default, the SDK selects the appropriate ASP.NET Core or .NET runtime base image from Microsoft Container Registry (mcr.microsoft.com). You can override this behavior using MSBuild properties.

4. **Layer Construction**: The SDK constructs container layers intelligently, separating the runtime, application binaries, and static assets into distinct layers for optimal caching and pull performance.

5. **OCI Image Generation**: The final OCI-compliant image is created, complete with manifest, configuration, and layer blobs—all without invoking any external container runtime.

### Key Capabilities in .NET 10

**Multi-Platform Support**: Target multiple architectures in a single command:
```bash
dotnet publish --os linux --arch x64 /t:PublishContainer
dotnet publish --os linux --arch arm64 /t:PublishContainer
```

**Direct Registry Publishing**: Push directly to Azure Container Registry without local image storage:
```bash
dotnet publish /t:PublishContainer \
  -p ContainerRegistry=myregistry.azurecr.io \
  -p ContainerRepository=myapp \
  -p ContainerImageTag=1.0.0
```

**Tarball Export**: Generate container images as portable tarballs for security scanning or air-gapped environments:
```bash
dotnet publish /t:PublishContainer \
  -p ContainerArchiveOutputPath=./output/image.tar.gz
```

**Native AOT Support**: For scenarios demanding minimal image sizes and sub-millisecond startup times, .NET 10's Native AOT compilation can reduce container images to as small as 10MB with 1-2ms cold start latency.

### Azure Integration Excellence

The .NET SDK's native container publishing integrates deeply with Azure services. When combined with `dotnet publish` properties, you can:

- **Authenticate via Azure CLI**: The SDK respects `az acr login` credentials, enabling secure pushes without embedding secrets
- **Leverage Managed Identity**: In Azure DevOps or GitHub Actions workflows, the SDK can use workload identity federation for passwordless authentication
- **Generate SBOMs**: Software Bill of Materials generation can be integrated directly into the publish process for supply chain compliance

### Why This Matters for Azure Deployments

For organizations invested in Azure, the SDK-native approach eliminates several layers of complexity:

- **No Docker Engine Required**: Build agents can be lighter, with fewer security vulnerabilities
- **Consistent Builds**: The same `dotnet publish` command works across development, CI/CD, and production environments
- **Faster CI/CD Pipelines**: By removing Docker daemon overhead, pipeline execution times decrease by 30-40% in typical scenarios
- **Native ACR Integration**: Direct pushes to Azure Container Registry leverage Azure's network infrastructure for optimal performance

This approach represents the future of .NET containerization—where the distinction between "building an application" and "building a container" disappears entirely.

---

## 3. 🐳 Traditional Dockerfile with Docker: The Classic Approach

While SDK-native publishing represents the future, the traditional Dockerfile approach with Docker remains the industry standard for good reason. This method provides unparalleled control over every aspect of the container build process, from base image selection to layer optimization. The Dockerfile format, now over a decade old, has evolved into a sophisticated domain-specific language that enables multi-stage builds, build-time arguments, and conditional logic based on build contexts.

### Why This Approach Endures

In enterprise environments, the Dockerfile approach continues to thrive because it offers something SDK-native publishing cannot: complete transparency. Every layer is explicitly defined, every dependency is documented in code, and the build process is fully observable. For organizations subject to regulatory compliance or those running mission-critical workloads, this visibility is non-negotiable.

### The Multi-Stage Build Pattern

The canonical .NET Dockerfile employs multi-stage builds to separate the build environment from the runtime environment:

```dockerfile
FROM mcr.microsoft.com/dotnet/sdk:10.0 AS build
WORKDIR /app
COPY . .
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/aspnet:10.0
WORKDIR /app
COPY --from=build /app/out .
ENTRYPOINT ["dotnet", "MyApp.dll"]
```

This pattern reduces final image size by excluding the SDK—which can exceed 1GB—from the production image. The result is a lean runtime image typically under 200MB.

### Azure Container Registry Integration

Docker's integration with Azure Container Registry is mature and battle-tested. The workflow is straightforward:

```bash
# Authenticate using Azure CLI
az acr login --name myregistry

# Build with context
docker build -t myapp:latest .

# Tag for ACR
docker tag myapp:latest myregistry.azurecr.io/myapp:latest

# Push to registry
docker push myregistry.azurecr.io/myapp:latest
```

For CI/CD pipelines, Azure DevOps provides dedicated tasks for Docker build and push operations, with built-in support for service connections and managed identities.

### Advanced Optimization Techniques

**Layer Caching**: Ordering Dockerfile commands strategically can dramatically accelerate CI/CD builds. Copying project files and restoring dependencies before copying source code ensures that the `dotnet restore` layer is cached unless dependencies change.

**Build Arguments**: Parameterize Dockerfiles for different environments:
```dockerfile
ARG ENVIRONMENT=Production
RUN dotnet publish -c Release -o out -p:Environment=$ENVIRONMENT
```

**.dockerignore**: Excluding bin/, obj/, and .git directories prevents unnecessary context uploads to the Docker daemon, reducing build times by 50% or more.

### Use Cases

The Dockerfile approach is ideal for:
- Complex applications requiring custom base images
- Organizations with established Docker-based CI/CD pipelines
- Scenarios requiring multi-container orchestration with docker-compose
- Teams needing fine-grained control over container configuration

---

## 4. 🔐 Traditional Dockerfile with Podman: The Daemonless Alternative

Podman represents a fundamental shift in container runtime architecture. Unlike Docker, which relies on a centralized daemon with root privileges, Podman operates as a daemonless, rootless container engine. For organizations prioritizing security—particularly in Azure environments where containers run on shared infrastructure—Podman offers compelling advantages without sacrificing compatibility.

### The Security Advantage

The daemonless architecture eliminates the single point of failure inherent in Docker's model. Each Podman command forks a new process, and containers run under the user's namespace, preventing privilege escalation attacks. In Azure DevOps pipelines, this means containers can build and run without requiring sudo access on build agents—a significant security improvement for shared environments.

### Seamless Docker Compatibility

Podman's command-line interface is designed as a drop-in replacement for Docker. Most Docker commands work identically with Podman:

```bash
# Build an image (same as Docker)
podman build -t myapp:latest .

# Tag for ACR
podman tag myapp:latest myregistry.azurecr.io/myapp:latest

# Push to Azure Container Registry
podman push myregistry.azurecr.io/myapp:latest
```

### Podman Desktop for Windows

Microsoft and Red Hat have collaborated to bring Podman Desktop to Windows, providing a GUI experience comparable to Docker Desktop. For .NET developers on Windows, this means you can leverage Podman's security benefits while maintaining the familiar workflow of Visual Studio 2026. Podman Desktop integrates with the Windows Subsystem for Linux (WSL), providing seamless file system sharing and networking.

### Rootless Containers in Practice

Rootless containers, Podman's default operating mode, have profound implications for Azure deployments:

**Reduced Attack Surface**: Containers running under user namespaces cannot escalate to host root privileges, even if the application inside the container is compromised.

**Multi-Tenant Safety**: In shared development environments or CI/CD agents, rootless containers prevent one user's containers from affecting another's.

**Compliance**: For organizations adhering to strict security frameworks like NIST or CIS Benchmarks, rootless containers address multiple control requirements.

### Azure Container Registry Integration

Podman integrates with Azure Container Registry through standard OCI authentication mechanisms:

```bash
# Login using Azure CLI credentials
az acr login --name myregistry
podman login myregistry.azurecr.io -u 00000000-0000-0000-0000-000000000000 \
  -p $(az acr login --name myregistry --expose-token --output tsv --query accessToken)
```

For automated pipelines, workload identity federation provides passwordless authentication, eliminating credential management overhead.

### Use Cases

Podman excels in:
- Security-conscious Azure deployments requiring rootless containers
- CI/CD environments where Docker daemon installation is restricted
- Development teams transitioning from Docker to open-source alternatives
- Scenarios requiring systemd integration for containerized services

---

## 5. ⚡ Azure Developer CLI (azd) with .NET Aspire: The Turnkey Solution

The Azure Developer CLI (`azd`) combined with .NET Aspire represents the most opinionated and automated approach to deploying .NET 10 applications to Azure. This toolchain embodies Microsoft's vision for the future of cloud development: infrastructure as code, containerization, and deployment orchestrated through a single command.

### The .NET Aspire Foundation

.NET Aspire, introduced with .NET 8 and matured in .NET 10, is an opinionated cloud-native development framework that includes built-in service discovery, health checks, telemetry, and container orchestration patterns. When combined with `azd`, it transforms complex Azure deployments into a deterministic, repeatable process.

### One Command to Rule Them All

The `azd up` command orchestrates three distinct phases:

1. **Package**: `azd package` analyzes your .NET Aspire project, identifies all constituent services (API endpoints, frontend applications, databases, caches), and builds container images for each using the SDK-native publishing method discussed earlier.

2. **Provision**: `azd provision` uses Bicep or ARM templates to create Azure resources. A typical .NET Aspire deployment provisions:
   - Azure Container Registry for image storage
   - Azure Container Apps Environment for serverless hosting
   - Log Analytics Workspace for centralized logging
   - Application Insights for monitoring
   - Azure Redis Cache for distributed caching
   - Azure Key Vault for secrets management

3. **Deploy**: `azd deploy` pushes container images to ACR and creates or updates revisions in Azure Container Apps, ensuring zero-downtime deployments with traffic shifting.

### Infrastructure as Code Without the Complexity

One of `azd`'s most powerful features is its ability to generate Bicep infrastructure files automatically from your .NET Aspire configuration. The `azd init` command analyzes your solution and creates:

- `main.bicep`: Core infrastructure definition
- `resources.bicep`: Service-specific resources
- `azure.yaml`: Deployment workflow configuration

This approach democratizes infrastructure management, allowing .NET developers to define cloud resources using familiar .NET constructs rather than learning a separate infrastructure language.

### Podman and Docker Support

`azd` automatically detects your container runtime and uses whatever is available—Podman, Docker, or even no runtime (for tarball exports). This flexibility ensures that the same development workflow works across Linux build agents, Windows workstations, and macOS laptops.

### Multi-Environment Support

The `azd` ecosystem includes built-in environment management:

```bash
# Create development environment
azd env new dev
azd env set AZURE_LOCATION eastus
azd up

# Create production environment
azd env new prod
azd env set AZURE_LOCATION westus3
azd env set AZURE_SKU Production
azd up
```

Each environment gets its own Azure resources, configuration variables, and deployment history—enabling true infrastructure isolation.

### Use Cases

`azd` with .NET Aspire is perfect for:
- Greenfield applications where infrastructure requirements are well-defined
- Teams adopting platform engineering practices
- Organizations standardizing on Azure Container Apps
- Developers who prefer convention over configuration

---

## 6. 🖱️ Visual Studio 2026 GUI Publishing: Drag-and-Drop Azure Deployments

For developers who prefer visual tooling, Visual Studio 2026 offers the most accessible path to Azure container deployment. The IDE's publishing workflow abstracts the complexity of container builds and registry authentication behind an intuitive GUI, making containerization accessible to developers who might otherwise be intimidated by command-line tools.

### The Visual Studio Container Experience

When you create a new ASP.NET Core project in Visual Studio 2026, you're presented with the option to "Enable Container Support." Selecting this option does more than add a Dockerfile to your project—it configures your entire development experience for containerized workflows:

- **Integrated Debugging**: Visual Studio attaches the debugger to containers running in Podman or Docker, supporting breakpoints, watch windows, and edit-and-continue (Hot Reload) inside the container environment.

- **Container Tool Window**: A dedicated tool window shows running containers, images, logs, and resource usage—all within the Visual Studio interface.

- **Container Orchestration Support**: For multi-container applications, Visual Studio can generate docker-compose files and manage the entire stack with a single click.

### The Publish Workflow

Publishing a containerized application to Azure from Visual Studio follows a guided wizard:

1. **Right-click the project** and select "Publish"
2. **Select target**: Choose "Docker Container Registry" from the list
3. **Select Azure Container Registry**: Visual Studio enumerates your ACR instances and allows you to create new ones
4. **Configure image details**: Set repository name, tags, and base image options
5. **Publish**: Visual Studio builds the container image (using Podman or Docker) and pushes it to ACR

### Podman as First-Class Citizen

Visual Studio 2026 recognizes Podman Desktop as a valid container runtime. If Podman is installed and configured, Visual Studio uses it automatically, falling back to Docker only if Podman is unavailable. This means you can develop on Windows with Podman's enhanced security while maintaining the same GUI experience.

### Hot Reload Inside Containers

One of the most significant productivity features in Visual Studio 2026 is Hot Reload support for containerized applications. When you modify code while debugging a containerized app, Visual Studio applies changes to the running container without requiring a rebuild or restart. For .NET 10 applications, this includes support for Blazor components, minimal APIs, and Razor pages.

### Integration with Azure DevOps

Visual Studio's publish workflow can also create Azure Pipelines YAML files that replicate the deployment process. After publishing successfully, Visual Studio offers to commit the pipeline configuration to your repository, enabling continuous delivery with minimal effort.

### Use Cases

Visual Studio GUI publishing excels in:
- Development teams with mixed skill levels
- Rapid prototyping and proof-of-concept deployments
- Organizations standardized on Windows workstations
- Scenarios requiring integrated debugging of containerized applications

---

## 7. 🔒 Tarball Export + Runtime Load: Security-First CI/CD Workflows

In regulated industries and security-conscious organizations, deploying container images directly to registries is often prohibited. Instead, images must pass through security gates—vulnerability scanning, license compliance checks, and approval workflows—before they can be pushed to production registries. The tarball export capability in .NET SDK's native container publishing addresses this requirement perfectly.

```mermaid
```

![7. 🔒 Tarball Export + Runtime Load: Security-First CI/CD Workflows](https://raw.githubusercontent.com/Vineet-Sharma-Medium-Stories/Medium-Build-Tool/refs/heads/main/Publishing%20.NET%2010%20Apps%20as%20Container%20Images/publishing-net-10-apps-as-container-images-a-complete-guide-to-9-deployment-approaches/images/diagram_03_in-regulated-industries-and-security-conscious-org-8794.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/publishing-net-10-apps-as-container-images-a-complete-guide-to-9-deployment-approaches/diagram_03_in-regulated-industries-and-security-conscious-org-8794.md)


### The Air-Gapped Workflow

The tarball export approach decouples image creation from image distribution, enabling sophisticated security controls:

**Stage 1: Build and Export**
```bash
# Build container image as tarball without container runtime
dotnet publish --os linux --arch x64 \
    /t:PublishContainer \
    -p ContainerArchiveOutputPath=./output/myapp.tar.gz
```

**Stage 2: Security Scanning**
```bash
# Scan the tarball for vulnerabilities
trivy image --input ./output/myapp.tar.gz

# Check license compliance
grype ./output/myapp.tar.gz

# Generate SBOM for supply chain verification
syft ./output/myapp.tar.gz -o spdx-json > sbom.json
```

**Stage 3: Approval Workflow**
The tarball and scan results proceed through an approval process. This might involve:
- Manual review by security teams
- Automated policy evaluation (e.g., "no critical vulnerabilities")
- Signing with Cosign or Notary for provenance verification

**Stage 4: Load and Push**
```bash
# Load the approved tarball into container runtime
podman load -i ./output/myapp.tar.gz
# or
docker load -i ./output/myapp.tar.gz

# Push to production Azure Container Registry
podman push myapp:latest myregistry.azurecr.io/myapp:latest
```

### Benefits for Compliance

This approach addresses several compliance requirements:

**NIST SP 800-190**: Container security standards require that images be scanned before deployment—a requirement met by the tarball workflow.

**PCI DSS**: Payment Card Industry standards mandate that all software components be verified before entering production environments.

**FedRAMP**: Federal Risk and Authorization Management Program requires continuous monitoring and approval workflows that align with this pattern.

### Azure Integration for Tarball Workflows

Azure provides services that complement this security-first approach:

**Azure Container Registry Tasks**: ACR can import tarballs directly, allowing you to push approved images without loading them into a local runtime:
```bash
az acr import --name myregistry \
    --source myapp.tar.gz \
    --image myapp:approved
```

**Microsoft Defender for Cloud**: Scans images in ACR automatically when they're pushed, providing continuous vulnerability assessment.

**Azure Key Vault**: Store signatures and attestation documents for signed images, enabling admission controller validation in AKS.

### Performance Characteristics

Tarball exports have minimal overhead because the .NET SDK generates them without invoking a container runtime. Export times are typically 10-20% longer than direct registry pushes due to compression, but this trade-off is acceptable for security-controlled environments.

### Use Cases

Tarball export is essential for:
- Regulated industries (finance, healthcare, government)
- Air-gapped or disconnected environments
- CI/CD pipelines with mandatory security gates
- Organizations implementing supply chain security frameworks

---

## 8. 🔄 Podman with .NET SDK Native Publishing: Hybrid Workflows

The combination of .NET SDK-native container publishing with Podman represents a hybrid approach that offers the best of both worlds: the simplicity of SDK-native builds with the flexibility of Podman's local development and testing capabilities.

```mermaid
```

![8. 🔄 Podman with .NET SDK Native Publishing: Hybrid Workflows](https://raw.githubusercontent.com/Vineet-Sharma-Medium-Stories/Medium-Build-Tool/refs/heads/main/Publishing%20.NET%2010%20Apps%20as%20Container%20Images/publishing-net-10-apps-as-container-images-a-complete-guide-to-9-deployment-approaches/images/diagram_04_the-combination-of-net-sdk-native-container-publi-ceb8.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/publishing-net-10-apps-as-container-images-a-complete-guide-to-9-deployment-approaches/diagram_04_the-combination-of-net-sdk-native-container-publi-ceb8.md)


### The Hybrid Workflow

This approach uses the SDK for image creation but relies on Podman for local testing and final registry pushes:

```bash
# Build the image using .NET SDK (no runtime required)
dotnet publish --os linux --arch x64 /t:PublishContainer

# Verify the image in local Podman registry
podman images | grep myapp

# Test the container locally
podman run -p 8080:8080 myapp:latest

# Push to Azure Container Registry
podman push myapp:latest myregistry.azurecr.io/myapp:latest
```

### Why Combine Both Approaches?

The hybrid model addresses scenarios where:

**SDK-Native Builds Are Preferred**: You want to avoid Dockerfile maintenance and leverage SDK-native optimizations like assembly trimming and Native AOT.

**Podman Is Required for Operations**: Your organization has standardized on Podman for container runtime operations, or you need rootless containers for security compliance.

**Local Testing Is Essential**: You need to test the exact image that will be deployed, not a facsimile built by a different process.

### Multi-Architecture Testing

One powerful capability of this hybrid approach is testing multi-architecture images locally. Podman can emulate different CPU architectures using QEMU, allowing you to validate ARM64 images on x64 development machines:

```bash
# Build ARM64 image with SDK
dotnet publish --os linux --arch arm64 /t:PublishContainer

# Run ARM64 image on x64 using Podman emulation
podman run --platform linux/arm64 -p 8080:8080 myapp:latest
```

### Azure Container Registry Authentication

When pushing from Podman to ACR, you can leverage Azure's native authentication methods:

```bash
# Option 1: Azure CLI integration
az acr login --name myregistry
podman login myregistry.azurecr.io -u 00000000-0000-0000-0000-000000000000 \
  -p $(az acr login --name myregistry --expose-token --output tsv --query accessToken)

# Option 2: Managed identity (for CI/CD)
podman login myregistry.azurecr.io -u $(az identity show -n myidentity -g mygroup --query principalId -o tsv) \
  -p $(az account get-access-token --resource https://myregistry.azurecr.io --query accessToken -o tsv)
```

### Development Inner Loop

For developers, the hybrid workflow enables rapid iteration:

1. Make code changes in Visual Studio or VS Code
2. Run `dotnet publish /t:PublishContainer` to build new image
3. Use Podman to run the image and test changes
4. Iterate without pushing to a remote registry until ready

### Use Cases

The hybrid approach is ideal for:
- Teams transitioning from Docker to Podman
- Development environments requiring local testing of SDK-native images
- Multi-architecture development workflows
- Organizations using Podman for production but wanting SDK-native simplicity

---

## 9. 🛠️ konet: Multi-Platform Container Builds Without Docker

konet (pronounced "ko-net") is a .NET global tool that represents a third generation of container build tooling. Unlike the .NET SDK's native container publishing, which requires the .NET SDK to be present, konet can build container images from compiled .NET applications without requiring the SDK or any container runtime. This makes it uniquely suited for scenarios where build environments must be minimal or where .NET SDK version consistency is critical.

### The konet Philosophy

konet treats container images as outputs that can be generated from any .NET build artifact, not just those produced by the current build. This separation of concerns enables:

- **SDK-Agnostic Builds**: Build .NET applications with one version of the SDK, package them with konet using a different version, or even after the SDK has been removed from the environment.

- **Cross-Platform from Anywhere**: Generate Linux containers from Windows build agents, ARM64 images from x64 machines, or Windows containers from Linux agents—all without emulation or cross-compilation complexities.

- **Artifact Reuse**: Build once, package multiple times. The same compiled application can be packaged into multiple container variants (different base images, different tags) without rebuilding.

### Basic Usage

```bash
# Install konet globally
dotnet tool install --global konet

# Build a container image from published application
konet build \
  --publish-path ./publish \
  --output myapp:latest \
  --base-image mcr.microsoft.com/dotnet/aspnet:10.0
```

### Multi-Platform Simultaneous Builds

konet's most powerful feature is its ability to build images for multiple architectures in parallel:

```bash
konet build \
  --publish-path ./publish \
  --output myapp:latest \
  --platforms linux/amd64,linux/arm64 \
  --registry myregistry.azurecr.io
```

This command builds both AMD64 and ARM64 images, creates a multi-architecture manifest, and pushes everything to Azure Container Registry in one operation—all without requiring either architecture's native build environment.

```mermaid
```

![Diagram](https://raw.githubusercontent.com/Vineet-Sharma-Medium-Stories/Medium-Build-Tool/refs/heads/main/Publishing%20.NET%2010%20Apps%20as%20Container%20Images/publishing-net-10-apps-as-container-images-a-complete-guide-to-9-deployment-approaches/images/diagram_05_this-command-builds-both-amd64-and-arm64-images-c-d857.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/publishing-net-10-apps-as-container-images-a-complete-guide-to-9-deployment-approaches/diagram_05_this-command-builds-both-amd64-and-arm64-images-c-d857.md)


### Azure Integration Features

konet includes Azure-specific features that streamline deployment:

**ACR Authentication**: konet integrates with Azure CLI and managed identity workflows:
```bash
# Uses your existing Azure CLI login
konet build --push --registry myregistry.azurecr.io
```

**OCI Artifact Support**: Beyond container images, konet can push any file as an OCI artifact to ACR, enabling storage of Helm charts, policy bundles, or vulnerability scan results alongside your images.

**SBOM Generation**: konet automatically generates SPDX-compliant SBOMs and attaches them as OCI artifacts, satisfying supply chain security requirements.

### Performance Advantages

Because konet operates on compiled artifacts rather than source code, it can be dramatically faster than Dockerfile-based builds:

- **No dependency restoration**: Packages are already restored during the build phase
- **No source code transmission**: Only compiled binaries are processed
- **Parallel layer generation**: Multiple architectures build simultaneously

### Use Cases

konet excels in:
- CI/CD pipelines where .NET SDK and Docker both present challenges
- Scenarios requiring multi-architecture images without emulation
- Build environments with strict security controls (no container runtime)
- Organizations adopting supply chain security practices (SBOM generation)

---

### Stories at a Glance

**Companion stories in this series:**

- 📚 **1. .NET SDK Native Container Publishing Deep Dive: The Complete Reference** – Comprehensive coverage of MSBuild properties, Native AOT optimization, CI/CD pipeline patterns, performance benchmarks, and troubleshooting guides

- 🚀 **2. .NET SDK Native Container Publishing: Building OCI Images Without Docker** – A deep dive into MSBuild configuration, multi-architecture builds, Native AOT optimization, and direct Azure Container Registry integration with workload identity federation

- 🐳 **3. Traditional Dockerfile with Docker: The Classic Approach** – Mastering multi-stage builds, build cache optimization, .dockerignore patterns, and Azure Container Registry authentication for enterprise CI/CD pipelines

- 🔐 **4. Traditional Dockerfile with Podman: The Daemonless Alternative** – Transitioning from Docker to Podman, rootless containers for enhanced security, podman-compose workflows, and Azure ACR integration with Podman Desktop

- ⚡ **5. Azure Developer CLI (azd) with .NET Aspire: The Turnkey Solution** – Full-stack deployments with `azd up`, Azure Container Apps provisioning, Redis caching, and infrastructure-as-code with Bicep templates

- 🖱️ **6. Visual Studio 2026 GUI Publishing: Drag-and-Drop Azure Deployments** – Leveraging Visual Studio's built-in Podman/Docker support, one-click publish to Azure Container Registry, and debugging containerized apps with Hot Reload

- 🔒 **7. Tarball Export + Runtime Load: Security-First CI/CD Workflows** – Generating container tarballs without a runtime, integrating with Trivy/Grype for vulnerability scanning, and deploying to air-gapped Azure environments

- 🔄 **8. Podman with .NET SDK Native Publishing: Hybrid Workflows** – Combining SDK-native builds with Podman for local testing, multi-architecture emulation, and Azure Container Registry push strategies

- 🛠️ **9. konet: Multi-Platform Container Builds Without Docker** – Using the konet .NET tool for cross-platform image generation, ARM64/AMD64 simultaneous builds, and GitHub Actions optimization

---

## What's Next?

Over the coming weeks, each approach in this series will be explored in exhaustive detail. We'll examine real-world Azure deployment scenarios, benchmark performance across methods, and provide production-ready patterns for CI/CD pipelines. Whether you're a startup deploying your first containerized application or an enterprise migrating thousands of workloads to Azure, you'll find practical guidance tailored to your infrastructure requirements.

The evolution from Dockerfile-centric builds to SDK-native containerization reflects a maturing ecosystem where .NET 10 stands at the forefront of developer experience. By mastering these nine approaches, you'll be equipped to choose the right tool for every scenario—from rapid prototyping to mission-critical production deployments on Azure.

---

**Coming next in the series:**
**📚 .NET SDK Native Container Publishing Deep Dive: The Complete Reference** – We'll explore MSBuild properties that control every aspect of image generation, demonstrate multi-architecture pipelines in Azure DevOps, and show how to reduce image sizes by 90% using Native AOT and assembly trimming.