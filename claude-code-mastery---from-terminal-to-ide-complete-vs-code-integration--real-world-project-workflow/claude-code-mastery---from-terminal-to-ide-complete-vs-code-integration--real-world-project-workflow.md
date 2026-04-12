# Claude Code Mastery - From Terminal to IDE : Complete VS Code Integration & Real-World Project Workflo

### A deep dive into project memory, security boundaries, surgical precision with Plan Mode, and the safety net of automatic Git snapshots.

## ![From Terminal to IDE](./images/From-Terminal-to-IDE.png)

# Introduction: Bridging the Gap Between AI and IDE

Throughout this series, we've explored Claude Code's powerful features—from memory and permissions to advanced workflows and parallel execution. But the true magic happens when Claude Code becomes an integral part of your development environment, working seamlessly alongside your IDE, tools, and existing workflows.

This final story takes you on a complete journey from zero to production, showing how Claude Code integrates with VS Code to build a real-world microservices project. You'll learn how to:

- Set up Claude Code as a first-class citizen in VS Code
- Create a complete production-ready application from scratch
- Establish development workflows that leverage both Claude Code and IDE features
- Deploy to production with confidence using everything we've learned

```mermaid
```

![Diagram](images/diagram_01_deploy-to-production-with-confidence-using-every-ae75.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/claude-code-mastery---from-terminal-to-ide-complete-vs-code-integration--real-world-project-workflow/diagram_01_deploy-to-production-with-confidence-using-every-ae75.md)


---

### Complete Claude Code Mastery Series:

- 🧠 **[1. Claude Code Mastery - The Memory & Control Layer: CLAUDE.md, Permissions, Plan Mode, and Checkpoints](#)** – A deep dive into project memory, security boundaries, surgical precision with Plan Mode, and the safety net of automatic Git snapshots. *Comming soon
- 🔧 **[2. Claude Code Mastery - The Extension & Integration Framework: Skills, Hooks, MCP, and Plugins](#)** – How to build reusable instructions, trigger automated workflows, connect Claude to external databases/APIs, and extend functionality with community plugins. *Comming soon
- ⚡ **[3. Claude Code Mastery - The Advanced Workflow Engine: Context Management, Slash Commands, Compaction, and Subagents](#)** – Mastering parallel execution, custom command shortcuts, token optimization strategies, and dividing complex tasks into scalable AI workflows. *Comming soon
- 🏗️ **[4. Claude Code Mastery - From Terminal to IDE: Complete VS Code Integration & Real-World Project Workflow](#)** – A hands-on guide to integrating Claude Code with VS Code, building a complete microservices project from scratch, and establishing production-ready development workflows. *Comming soon*(This story)*

---

## Part 1: Setting Up the Development Environment

### Step 1: VS Code Configuration for Claude Code

#### Install Essential Extensions

```bash
# Install VS Code extensions for optimal Claude Code experience
code --install-extension ms-python.python
code --install-extension ms-python.vscode-pylance
code --install-extension ms-python.black-formatter
code --install-extension ms-python.flake8
code --install-extension eamodio.gitlens
code --install-extension github.copilot
code --install-extension github.copilot-chat
code --install-extension ms-azuretools.vscode-docker
code --install-extension ms-vscode-remote.remote-containers
```

#### Create VS Code Workspace Settings

Create `.vscode/settings.json` in your project root:

```json
{
  "python.defaultInterpreterPath": "${workspaceFolder}/venv/bin/python",
  "python.terminal.activateEnvironment": true,
  "python.formatting.provider": "black",
  "python.formatting.blackArgs": ["--line-length", "88"],
  "python.linting.enabled": true,
  "python.linting.flake8Enabled": true,
  "python.linting.mypyEnabled": true,
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.organizeImports": true
  },
  "files.associations": {
    "CLAUDE.md": "markdown",
    "*.skill.md": "markdown"
  },
  "[markdown]": {
    "editor.wordWrap": "on",
    "editor.quickSuggestions": false
  },
  "terminal.integrated.shellArgs.linux": ["-l"],
  "terminal.integrated.defaultProfile.windows": "PowerShell",
  "git.autofetch": true,
  "gitlens.codeLens.enabled": true,
  "workbench.colorTheme": "Default Dark+",
  "editor.fontSize": 14,
  "terminal.integrated.fontSize": 13
}
```

#### Create VS Code Tasks for Claude Integration

Create `.vscode/tasks.json`:

```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "Claude: Start Session",
      "type": "shell",
      "command": "claude",
      "group": "none",
      "presentation": {
        "reveal": "always",
        "panel": "new"
      },
      "problemMatcher": []
    },
    {
      "label": "Claude: Code Review",
      "type": "shell",
      "command": "claude -c '/review ${file}'",
      "group": "none",
      "presentation": {
        "reveal": "always",
        "panel": "dedicated"
      }
    },
    {
      "label": "Claude: Generate Tests",
      "type": "shell",
      "command": "claude -c '/test generate ${file}'",
      "group": "none",
      "presentation": {
        "reveal": "always",
        "panel": "dedicated"
      }
    },
    {
      "label": "Claude: Fix Issues",
      "type": "shell",
      "command": "claude -c '/fix ${file}'",
      "group": "none",
      "presentation": {
        "reveal": "always",
        "panel": "dedicated"
      }
    },
    {
      "label": "Run All Tests",
      "type": "shell",
      "command": "pytest tests/ -v --cov=src --cov-report=html",
      "group": "test",
      "presentation": {
        "reveal": "always",
        "panel": "dedicated"
      }
    },
    {
      "label": "Start Development Server",
      "type": "shell",
      "command": "uvicorn src.main:app --reload --host 0.0.0.0 --port 8000",
      "group": "none",
      "presentation": {
        "reveal": "always",
        "panel": "dedicated"
      },
      "isBackground": true
    }
  ]
}
```

#### Create VS Code Launch Configuration for Debugging

Create `.vscode/launch.json`:

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Python: FastAPI",
      "type": "python",
      "request": "launch",
      "module": "uvicorn",
      "args": ["src.main:app", "--reload", "--port", "8000"],
      "jinja": true,
      "justMyCode": true
    },
    {
      "name": "Python: Current File",
      "type": "python",
      "request": "launch",
      "program": "${file}",
      "console": "integratedTerminal"
    },
    {
      "name": "Python: Pytest",
      "type": "python",
      "request": "launch",
      "module": "pytest",
      "args": ["${workspaceFolder}/tests", "-v"],
      "console": "integratedTerminal"
    }
  ]
}
```

### Step 2: Create Project Structure with Claude

Now let's use Claude Code to bootstrap our project:

```bash
# Start Claude Code
claude
```

**User Request:**

```
> Let's build a complete e-commerce microservices project. Create the project structure with:
  - Product Service (FastAPI)
  - Order Service (FastAPI)
  - User Service (FastAPI)
  - API Gateway
  - Shared libraries
  - Docker configuration
  - Kubernetes manifests
  - CI/CD pipeline
  - Comprehensive tests
  
  Follow best practices from all previous stories.
```

**Claude's Response:**

```
🏗️ Creating E-Commerce Microservices Project

Based on our previous sessions, I'll create a production-ready microservices architecture.

## Project Structure

```

ecommerce-microservices/  
├── .claude/  
│   ├── config.json  
│   ├── skills/  
│   ├── hooks/  
│   ├── commands/  
│   └── mcp/  
├── .vscode/  
│   ├── settings.json  
│   ├── tasks.json  
│   └── launch.json  
├── services/  
│   ├── product-service/  
│   │   ├── src/  
│   │   ├── tests/  
│   │   ├── Dockerfile  
│   │   └── requirements.txt  
│   ├── order-service/  
│   │   ├── src/  
│   │   ├── tests/  
│   │   ├── Dockerfile  
│   │   └── requirements.txt  
│   ├── user-service/  
│   │   ├── src/  
│   │   ├── tests/  
│   │   ├── Dockerfile  
│   │   └── requirements.txt  
│   └── api-gateway/  
│       ├── src/  
│       ├── tests/  
│       ├── Dockerfile  
│       └── requirements.txt  
├── shared/  
│   ├── models/  
│   ├── utils/  
│   └── schemas/  
├── kubernetes/  
│   ├── namespaces/  
│   ├── deployments/  
│   ├── services/  
│   └── ingress/  
├── docker-compose.yml  
├── Makefile  
├── CLAUDE.md  
├── README.md  
└── .github/  
    └── workflows/  
        ├── ci.yml  
        └── cd.yml

```

I'll now spawn subagents to create each service in parallel...

[Subagents working...]
```

---

## Part 2: Building the Microservices

### Step 1: Product Service Implementation

Let's see Claude create the product service with all features:

```bash
> Show me the product service implementation
```

**Claude's Output:**

```python
# services/product-service/src/main.py
"""
Product Service - FastAPI microservice for product catalog management.
Follows all project standards from CLAUDE.md.
"""

from fastapi import FastAPI, Depends, HTTPException, status
from fastapi.middleware.cors import CORSMiddleware
from fastapi.middleware.trustedhost import TrustedHostMiddleware
from contextlib import asynccontextmanager
import structlog
from sqlalchemy.ext.asyncio import AsyncSession
import redis.asyncio as redis

from src.api import products, categories, health
from src.core.database import init_db, close_db, get_db
from src.core.cache import init_cache, close_cache, get_cache
from src.core.config import settings
from src.core.logging import setup_logging
from src.middleware.auth import AuthMiddleware
from src.middleware.rate_limit import RateLimitMiddleware
from src.middleware.request_id import RequestIDMiddleware

logger = structlog.get_logger()

@asynccontextmanager
async def lifespan(app: FastAPI):
    """Handle startup and shutdown events."""
    # Startup
    logger.info("Starting product service...")
    await init_db()
    await init_cache()
    logger.info("Product service started", version=settings.VERSION)
    
    yield
    
    # Shutdown
    logger.info("Shutting down product service...")
    await close_db()
    await close_cache()
    logger.info("Product service stopped")

# Create FastAPI app
app = FastAPI(
    title="Product Service",
    version=settings.VERSION,
    description="E-commerce product catalog management",
    docs_url="/docs" if settings.DEBUG else None,
    redoc_url="/redoc" if settings.DEBUG else None,
    lifespan=lifespan
)

# Add middleware
app.add_middleware(RequestIDMiddleware)
app.add_middleware(RateLimitMiddleware, redis_client=redis.from_url(settings.REDIS_URL))
app.add_middleware(AuthMiddleware, excluded_paths=["/health", "/docs", "/openapi.json"])
app.add_middleware(
    CORSMiddleware,
    allow_origins=settings.CORS_ORIGINS,
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
app.add_middleware(
    TrustedHostMiddleware,
    allowed_hosts=settings.ALLOWED_HOSTS
)

# Include routers
app.include_router(health.router, prefix="/health", tags=["health"])
app.include_router(products.router, prefix="/api/v1/products", tags=["products"])
app.include_router(categories.router, prefix="/api/v1/categories", tags=["categories"])

@app.get("/")
async def root():
    """Root endpoint with service info."""
    return {
        "service": "product-service",
        "version": settings.VERSION,
        "status": "healthy",
        "documentation": "/docs" if settings.DEBUG else "not available"
    }
```

```python
# services/product-service/src/api/products.py
"""
Product API endpoints with comprehensive features.
"""

from typing import List, Optional
from fastapi import APIRouter, Depends, HTTPException, Query, status
from sqlalchemy.ext.asyncio import AsyncSession
from src.core.database import get_db
from src.core.cache import get_cache, CacheClient
from src.services.product_service import ProductService
from src.schemas.product import (
    ProductCreate,
    ProductUpdate,
    ProductResponse,
    ProductListResponse,
    ProductSearchParams
)
from src.middleware.auth import require_permission
import structlog

logger = structlog.get_logger()
router = APIRouter()

@router.post(
    "/",
    response_model=ProductResponse,
    status_code=status.HTTP_201_CREATED,
    summary="Create product",
    description="Create a new product in the catalog"
)
@require_permission("products:create")
async def create_product(
    product_data: ProductCreate,
    db: AsyncSession = Depends(get_db),
    cache: CacheClient = Depends(get_cache),
    current_user: dict = Depends(require_permission)
) -> ProductResponse:
    """
    Create a new product.
    
    - **name**: Product name (required)
    - **description**: Detailed description
    - **price**: Price in USD (must be > 0)
    - **stock_quantity**: Available stock (must be >= 0)
    - **category_id**: Category assignment
    - **metadata**: Additional product attributes
    """
    logger.info(
        "create_product_request",
        user_id=current_user["id"],
        product_name=product_data.name
    )
    
    service = ProductService(db, cache)
    
    try:
        product = await service.create_product_async(product_data)
        logger.info(
            "product_created",
            product_id=product.id,
            product_name=product.name
        )
        return product
    except ValueError as e:
        logger.warning("product_creation_failed", error=str(e))
        raise HTTPException(status_code=400, detail=str(e))
    except Exception as e:
        logger.error("unexpected_error", error=str(e), exc_info=True)
        raise HTTPException(
            status_code=500,
            detail="Internal server error"
        )

@router.get(
    "/search",
    response_model=ProductListResponse,
    summary="Search products",
    description="Search products with filters and pagination"
)
async def search_products(
    q: str = Query(..., min_length=2, description="Search query"),
    category_id: Optional[int] = Query(None, description="Filter by category"),
    min_price: Optional[float] = Query(None, ge=0, description="Minimum price"),
    max_price: Optional[float] = Query(None, ge=0, description="Maximum price"),
    in_stock: Optional[bool] = Query(None, description="Only in-stock items"),
    page: int = Query(1, ge=1, description="Page number"),
    page_size: int = Query(20, ge=1, le=100, description="Items per page"),
    sort_by: str = Query("relevance", description="Sort field"),
    db: AsyncSession = Depends(get_db),
    cache: CacheClient = Depends(get_cache)
) -> ProductListResponse:
    """
    Search products with full-text search and filters.
    
    Supports:
    - Full-text search on name and description
    - Category filtering
    - Price range filtering
    - Stock status filtering
    - Pagination
    - Sorting by relevance, price, name, or rating
    """
    logger.info(
        "product_search",
        query=q,
        category=category_id,
        page=page,
        page_size=page_size
    )
    
    search_params = ProductSearchParams(
        q=q,
        category_id=category_id,
        min_price=min_price,
        max_price=max_price,
        in_stock=in_stock,
        page=page,
        page_size=page_size,
        sort_by=sort_by
    )
    
    service = ProductService(db, cache)
    results = await service.search_products_async(search_params)
    
    logger.info(
        "product_search_completed",
        query=q,
        results_count=len(results.items),
        total=results.total
    )
    
    return results

@router.get(
    "/{product_id}",
    response_model=ProductResponse,
    summary="Get product by ID",
    description="Retrieve detailed product information"
)
async def get_product(
    product_id: int,
    db: AsyncSession = Depends(get_db),
    cache: CacheClient = Depends(get_cache)
) -> ProductResponse:
    """
    Get a product by its ID.
    
    Cached for 5 minutes to reduce database load.
    """
    logger.info("get_product_request", product_id=product_id)
    
    # Try cache first
    cache_key = f"product:{product_id}"
    cached = await cache.get(cache_key)
    
    if cached:
        logger.info("product_cache_hit", product_id=product_id)
        return ProductResponse.model_validate_json(cached)
    
    service = ProductService(db, cache)
    product = await service.get_product_async(product_id)
    
    if not product:
        logger.warning("product_not_found", product_id=product_id)
        raise HTTPException(
            status_code=404,
            detail=f"Product {product_id} not found"
        )
    
    # Cache for future requests
    await cache.set(cache_key, product.model_dump_json(), ttl=300)
    
    return product

@router.patch(
    "/{product_id}",
    response_model=ProductResponse,
    summary="Update product",
    description="Update product information"
)
@require_permission("products:update")
async def update_product(
    product_id: int,
    product_data: ProductUpdate,
    db: AsyncSession = Depends(get_db),
    cache: CacheClient = Depends(get_cache),
    current_user: dict = Depends(require_permission)
) -> ProductResponse:
    """
    Update an existing product.
    
    Only fields provided will be updated.
    """
    logger.info(
        "update_product_request",
        product_id=product_id,
        user_id=current_user["id"],
        updates=product_data.model_dump(exclude_unset=True)
    )
    
    service = ProductService(db, cache)
    
    try:
        product = await service.update_product_async(product_id, product_data)
        
        # Invalidate cache
        await cache.delete(f"product:{product_id}")
        
        logger.info("product_updated", product_id=product_id)
        return product
    except ValueError as e:
        raise HTTPException(status_code=404, detail=str(e))
    except Exception as e:
        logger.error("update_failed", error=str(e))
        raise HTTPException(status_code=500, detail="Update failed")

@router.delete(
    "/{product_id}",
    status_code=status.HTTP_204_NO_CONTENT,
    summary="Delete product",
    description="Soft delete a product"
)
@require_permission("products:delete")
async def delete_product(
    product_id: int,
    db: AsyncSession = Depends(get_db),
    cache: CacheClient = Depends(get_cache),
    current_user: dict = Depends(require_permission)
) -> None:
    """
    Soft delete a product (marks as deleted, doesn't remove from database).
    """
    logger.info(
        "delete_product_request",
        product_id=product_id,
        user_id=current_user["id"]
    )
    
    service = ProductService(db, cache)
    
    try:
        await service.delete_product_async(product_id)
        
        # Invalidate cache
        await cache.delete(f"product:{product_id}")
        
        logger.info("product_deleted", product_id=product_id)
    except ValueError as e:
        raise HTTPException(status_code=404, detail=str(e))
```

### Step 2: Docker Configuration with Multi-Stage Builds

```bash
> Create Dockerfiles for all services with multi-stage builds
```

**Claude's Output:**

```dockerfile
# services/product-service/Dockerfile
# Multi-stage build for product service

# Stage 1: Builder
FROM python:3.11-slim AS builder

WORKDIR /app

# Install build dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    gcc \
    libpq-dev \
    && rm -rf /var/lib/apt/lists/*

# Install Python dependencies
COPY requirements.txt .
RUN pip install --user --no-cache-dir -r requirements.txt

# Stage 2: Runtime
FROM python:3.11-slim

# Create non-root user
RUN groupadd -r app && useradd -r -g app app

WORKDIR /app

# Copy dependencies from builder
COPY --from=builder /root/.local /root/.local

# Copy application code
COPY src/ ./src/
COPY alembic/ ./alembic/
COPY alembic.ini .

# Make sure scripts in .local are usable
ENV PATH=/root/.local/bin:$PATH

# Set Python path
ENV PYTHONPATH=/app

# Security: Don't run as root
USER app

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
    CMD python -c "import requests; requests.get('http://localhost:8000/health')" || exit 1

# Expose port
EXPOSE 8000

# Start application
CMD ["uvicorn", "src.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### Step 3: Docker Compose for Local Development

Create `docker-compose.yml`:

```yaml
version: '3.8'

services:
  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_USER: ecommerce
      POSTGRES_PASSWORD: ecommerce123
      POSTGRES_DB: ecommerce
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ecommerce"]
      interval: 10s
      timeout: 5s
      retries: 5

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5

  product-service:
    build:
      context: ./services/product-service
      dockerfile: Dockerfile
    environment:
      DATABASE_URL: postgresql://ecommerce:ecommerce123@postgres:5432/ecommerce
      REDIS_URL: redis://redis:6379
      JWT_SECRET_KEY: ${JWT_SECRET_KEY}
      DEBUG: "true"
    ports:
      - "8001:8000"
    depends_on:
      postgres:
        condition: service_healthy
      redis:
        condition: service_healthy
    volumes:
      - ./services/product-service:/app
    command: uvicorn src.main:app --reload --host 0.0.0.0 --port 8000

  order-service:
    build:
      context: ./services/order-service
      dockerfile: Dockerfile
    environment:
      DATABASE_URL: postgresql://ecommerce:ecommerce123@postgres:5432/ecommerce
      REDIS_URL: redis://redis:6379
      JWT_SECRET_KEY: ${JWT_SECRET_KEY}
      PRODUCT_SERVICE_URL: http://product-service:8000
      DEBUG: "true"
    ports:
      - "8002:8000"
    depends_on:
      postgres:
        condition: service_healthy
      redis:
        condition: service_healthy
      product-service:
        condition: service_started
    volumes:
      - ./services/order-service:/app

  user-service:
    build:
      context: ./services/user-service
      dockerfile: Dockerfile
    environment:
      DATABASE_URL: postgresql://ecommerce:ecommerce123@postgres:5432/ecommerce
      REDIS_URL: redis://redis:6379
      JWT_SECRET_KEY: ${JWT_SECRET_KEY}
      DEBUG: "true"
    ports:
      - "8003:8000"
    depends_on:
      postgres:
        condition: service_healthy
      redis:
        condition: service_healthy
    volumes:
      - ./services/user-service:/app

  api-gateway:
    build:
      context: ./services/api-gateway
      dockerfile: Dockerfile
    environment:
      PRODUCT_SERVICE_URL: http://product-service:8000
      ORDER_SERVICE_URL: http://order-service:8000
      USER_SERVICE_URL: http://user-service:8000
      JWT_SECRET_KEY: ${JWT_SECRET_KEY}
      DEBUG: "true"
    ports:
      - "8000:8000"
    depends_on:
      - product-service
      - order-service
      - user-service
    volumes:
      - ./services/api-gateway:/app

volumes:
  postgres_data:
  redis_data:
```

### Step 4: Kubernetes Manifests

```bash
> Create Kubernetes deployment manifests for all services
```

**Claude's Output:**

```yaml
# kubernetes/namespaces/ecommerce.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: ecommerce
  labels:
    name: ecommerce
    environment: production

---
# kubernetes/deployments/product-service.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: product-service
  namespace: ecommerce
  labels:
    app: product-service
    tier: backend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: product-service
  template:
    metadata:
      labels:
        app: product-service
        tier: backend
    spec:
      containers:
      - name: product-service
        image: ${DOCKER_REGISTRY}/product-service:${VERSION}
        imagePullPolicy: Always
        ports:
        - containerPort: 8000
          name: http
        env:
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: database-secrets
              key: DATABASE_URL
        - name: REDIS_URL
          valueFrom:
            secretKeyRef:
              name: redis-secrets
              key: REDIS_URL
        - name: JWT_SECRET_KEY
          valueFrom:
            secretKeyRef:
              name: jwt-secrets
              key: JWT_SECRET_KEY
        resources:
          requests:
            memory: "256Mi"
            cpu: "250m"
          limits:
            memory: "512Mi"
            cpu: "500m"
        livenessProbe:
          httpGet:
            path: /health
            port: 8000
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /health/ready
            port: 8000
          initialDelaySeconds: 5
          periodSeconds: 5
      affinity:
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
          - weight: 100
            podAffinityTerm:
              labelSelector:
                matchExpressions:
                - key: app
                  operator: In
                  values:
                  - product-service
              topologyKey: kubernetes.io/hostname

---
# kubernetes/services/product-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: product-service
  namespace: ecommerce
  labels:
    app: product-service
spec:
  selector:
    app: product-service
  ports:
  - port: 8000
    targetPort: 8000
    name: http
  type: ClusterIP

---
# kubernetes/ingress/ingress.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ecommerce-ingress
  namespace: ecommerce
  annotations:
    kubernetes.io/ingress.class: nginx
    cert-manager.io/cluster-issuer: letsencrypt-prod
    nginx.ingress.kubernetes.io/rate-limit: "100"
    nginx.ingress.kubernetes.io/rate-limit-burst: "200"
spec:
  tls:
  - hosts:
    - api.ecommerce.com
    secretName: ecommerce-tls
  rules:
  - host: api.ecommerce.com
    http:
      paths:
      - path: /products
        pathType: Prefix
        backend:
          service:
            name: product-service
            port:
              number: 8000
      - path: /orders
        pathType: Prefix
        backend:
          service:
            name: order-service
            port:
              number: 8000
      - path: /users
        pathType: Prefix
        backend:
          service:
            name: user-service
            port:
              number: 8000
      - path: /
        pathType: Prefix
        backend:
          service:
            name: api-gateway
            port:
              number: 8000
```

---

## Part 3: CI/CD Pipeline with GitHub Actions

### Step 1: Continuous Integration Workflow

Create `.github/workflows/ci.yml`:

```yaml
name: CI Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

env:
  PYTHON_VERSION: '3.11'
  DOCKER_REGISTRY: ghcr.io
  DOCKER_IMAGE_NAME: ${{ github.repository }}

jobs:
  test:
    name: Test Services
    runs-on: ubuntu-latest
    strategy:
      matrix:
        service: [product-service, order-service, user-service, api-gateway]
    
    services:
      postgres:
        image: postgres:15-alpine
        env:
          POSTGRES_USER: test
          POSTGRES_PASSWORD: test
          POSTGRES_DB: test
        ports:
          - 5432:5432
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
      
      redis:
        image: redis:7-alpine
        ports:
          - 6379:6379
        options: >-
          --health-cmd "redis-cli ping"
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v3
    
    - name: Set up Python
      uses: actions/setup-python@v4
      with:
        python-version: ${{ env.PYTHON_VERSION }}
    
    - name: Cache pip packages
      uses: actions/cache@v3
      with:
        path: ~/.cache/pip
        key: ${{ runner.os }}-pip-${{ hashFiles('services/${{ matrix.service }}/requirements.txt') }}
        restore-keys: |
          ${{ runner.os }}-pip-
    
    - name: Install dependencies
      working-directory: services/${{ matrix.service }}
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
        pip install pytest pytest-cov pytest-asyncio black flake8 mypy
    
    - name: Lint with flake8
      working-directory: services/${{ matrix.service }}
      run: |
        flake8 src/ --count --select=E9,F63,F7,F82 --show-source --statistics
        flake8 src/ --count --exit-zero --max-complexity=10 --statistics
    
    - name: Check formatting with black
      working-directory: services/${{ matrix.service }}
      run: |
        black --check src/ tests/
    
    - name: Type check with mypy
      working-directory: services/${{ matrix.service }}
      run: |
        mypy src/ --ignore-missing-imports
    
    - name: Run tests with coverage
      working-directory: services/${{ matrix.service }}
      env:
        DATABASE_URL: postgresql://test:test@localhost:5432/test
        REDIS_URL: redis://localhost:6379
        JWT_SECRET_KEY: test-secret-key
      run: |
        pytest tests/ -v --cov=src --cov-report=xml --cov-report=html
    
    - name: Upload coverage to Codecov
      uses: codecov/codecov-action@v3
      with:
        file: services/${{ matrix.service }}/coverage.xml
        flags: ${{ matrix.service }}
        name: ${{ matrix.service }}-coverage

  security-scan:
    name: Security Scan
    runs-on: ubuntu-latest
    needs: test
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v3
    
    - name: Run Bandit security scanner
      run: |
        pip install bandit
        bandit -r services/ -f json -o bandit-report.json
        bandit -r services/ -f html -o bandit-report.html
    
    - name: Upload security report
      uses: actions/upload-artifact@v3
      with:
        name: security-reports
        path: bandit-report.*

  docker-build:
    name: Build Docker Images
    runs-on: ubuntu-latest
    needs: [test, security-scan]
    if: github.event_name == 'push' && github.ref == 'refs/heads/main'
    strategy:
      matrix:
        service: [product-service, order-service, user-service, api-gateway]
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v3
    
    - name: Set up Docker Buildx
      uses: docker/setup-buildx-action@v2
    
    - name: Log in to GitHub Container Registry
      uses: docker/login-action@v2
      with:
        registry: ${{ env.DOCKER_REGISTRY }}
        username: ${{ github.actor }}
        password: ${{ secrets.GITHUB_TOKEN }}
    
    - name: Extract metadata
      id: meta
      uses: docker/metadata-action@v4
      with:
        images: ${{ env.DOCKER_REGISTRY }}/${{ env.DOCKER_IMAGE_NAME }}/${{ matrix.service }}
        tags: |
          type=ref,event=branch
          type=sha,prefix={{branch}}-
          type=raw,value=latest,enable={{is_default_branch}}
    
    - name: Build and push Docker image
      uses: docker/build-push-action@v4
      with:
        context: services/${{ matrix.service }}
        push: true
        tags: ${{ steps.meta.outputs.tags }}
        labels: ${{ steps.meta.outputs.labels }}
        cache-from: type=gha
        cache-to: type=gha,mode=max
```

### Step 2: Continuous Deployment Workflow

Create `.github/workflows/cd.yml`:

```yaml
name: CD Pipeline

on:
  push:
    branches: [main]
    tags:
      - 'v*'

env:
  DOCKER_REGISTRY: ghcr.io
  KUBE_NAMESPACE: ecommerce

jobs:
  deploy-staging:
    name: Deploy to Staging
    runs-on: ubuntu-latest
    environment: staging
    if: github.ref == 'refs/heads/main'
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v3
    
    - name: Configure kubectl
      uses: azure/setup-kubectl@v3
      with:
        version: 'latest'
    
    - name: Set up Kubernetes config
      run: |
        mkdir -p $HOME/.kube
        echo "${{ secrets.STAGING_KUBE_CONFIG }}" | base64 --decode > $HOME/.kube/config
    
    - name: Deploy to staging
      run: |
        # Update images
        kubectl set image deployment/product-service product-service=${{ env.DOCKER_REGISTRY }}/${{ github.repository }}/product-service:latest -n ${{ env.KUBE_NAMESPACE }}
        kubectl set image deployment/order-service order-service=${{ env.DOCKER_REGISTRY }}/${{ github.repository }}/order-service:latest -n ${{ env.KUBE_NAMESPACE }}
        kubectl set image deployment/user-service user-service=${{ env.DOCKER_REGISTRY }}/${{ github.repository }}/user-service:latest -n ${{ env.KUBE_NAMESPACE }}
        kubectl set image deployment/api-gateway api-gateway=${{ env.DOCKER_REGISTRY }}/${{ github.repository }}/api-gateway:latest -n ${{ env.KUBE_NAMESPACE }}
        
        # Wait for rollout
        kubectl rollout status deployment/product-service -n ${{ env.KUBE_NAMESPACE }} --timeout=5m
        kubectl rollout status deployment/order-service -n ${{ env.KUBE_NAMESPACE }} --timeout=5m
        kubectl rollout status deployment/user-service -n ${{ env.KUBE_NAMESPACE }} --timeout=5m
        kubectl rollout status deployment/api-gateway -n ${{ env.KUBE_NAMESPACE }} --timeout=5m
    
    - name: Run smoke tests
      run: |
        ./scripts/smoke-test.sh https://staging.ecommerce.com

  deploy-production:
    name: Deploy to Production
    runs-on: ubuntu-latest
    environment: production
    needs: deploy-staging
    if: startsWith(github.ref, 'refs/tags/v')
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v3
    
    - name: Configure kubectl
      uses: azure/setup-kubectl@v3
      with:
        version: 'latest'
    
    - name: Set up Kubernetes config
      run: |
        mkdir -p $HOME/.kube
        echo "${{ secrets.PRODUCTION_KUBE_CONFIG }}" | base64 --decode > $HOME/.kube/config
    
    - name: Backup database
      run: |
        kubectl exec -n ${{ env.KUBE_NAMESPACE }} postgres-0 -- pg_dump -U ecommerce ecommerce > backup-$(date +%Y%m%d-%H%M%S).sql
    
    - name: Deploy with canary strategy
      run: |
        # Canary deployment (10% traffic)
        kubectl set image deployment/product-service-canary product-service=${{ env.DOCKER_REGISTRY }}/${{ github.repository }}/product-service:${{ github.ref_name }} -n ${{ env.KUBE_NAMESPACE }}
        kubectl rollout status deployment/product-service-canary -n ${{ env.KUBE_NAMESPACE }} --timeout=5m
        
        # Wait for canary validation
        sleep 300
        
        # Full deployment
        kubectl set image deployment/product-service product-service=${{ env.DOCKER_REGISTRY }}/${{ github.repository }}/product-service:${{ github.ref_name }} -n ${{ env.KUBE_NAMESPACE }}
        kubectl rollout status deployment/product-service -n ${{ env.KUBE_NAMESPACE }} --timeout=5m
    
    - name: Notify team
      uses: slackapi/slack-github-action@v1.24.0
      with:
        payload: |
          {
            "text": "✅ Production deployment successful!\nVersion: ${{ github.ref_name }}\nEnvironment: production\nDeployed by: ${{ github.actor }}"
          }
      env:
        SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
```

---

## Part 4: Complete Development Workflow

### The Ultimate Claude Code + VS Code Workflow

Now let's see how everything comes together in a complete development cycle:

```mermaid
```

![Part 4: Complete Development Workflow](images/diagram_02_now-lets-see-how-everything-comes-together-in-a-c-e60c.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/claude-code-mastery---from-terminal-to-ide-complete-vs-code-integration--real-world-project-workflow/diagram_02_now-lets-see-how-everything-comes-together-in-a-c-e60c.md)


### Hands-On Session: Building a New Feature

Let's walk through a complete development cycle:

```bash
# Start Claude Code in VS Code terminal
claude
```

**Step 1: Plan the Feature**

```
> /plan "Add bulk product import feature with CSV support, validation, and async processing"

📋 Creating plan for bulk product import...

## Feature: Bulk Product Import

### Components:
1. CSV Parser Service
2. Validation Engine
3. Async Task Queue (Celery)
4. Import API Endpoint
5. Progress Tracking
6. Error Reporting

### Files to Create:
- src/services/import_service.py
- src/services/csv_parser.py
- src/services/validator.py
- src/api/import.py
- src/tasks/import_tasks.py
- tests/unit/test_import.py
- tests/integration/test_bulk_import.py

### Database Changes:
- Add import_jobs table
- Add import_errors table

### Estimated: 1,200 lines, 4 subagents

Proceed? (yes/edit/no)
```

**Step 2: Spawn Subagents**

```
> yes

🚀 Starting implementation with 4 subagents...

[Import Service Agent] Creating import service... (25%)
[CSV Parser Agent] Implementing streaming parser... (40%)
[Validation Agent] Building validation rules... (60%)
[API Agent] Creating endpoints... (10%)

Progress: 34% complete
```

**Step 3: Monitor Progress in VS Code**

Use VS Code tasks to monitor:

```bash
# In another terminal
> /subagents status
```

**VS Code Output:**

```
📊 Subagent Status

┌─────────────────┬──────────┬────────────┬─────────────────────┐
│ Agent           │ Status   │ Progress   │ Current Task        │
├─────────────────┼──────────┼────────────┼─────────────────────┤
│ Import Service  │ Complete │ 100%       │ Service implemented │
│ CSV Parser      │ Running  │ 85%        │ Adding error handling│
│ Validation      │ Running  │ 70%        │ Creating rules engine│
│ API             │ Running  │ 45%        │ Writing endpoints   │
└─────────────────┴──────────┴────────────┴─────────────────────┘

Time elapsed: 3m 12s
```

**Step 4: Review Generated Code**

```bash
> /review src/services/import_service.py
```

**Claude's Review:**

```markdown
## Code Review: Import Service

### Overall: Good ✅

**Critical Issues: None**

**Major Issues 🟡:**
1. Add retry logic for failed imports (line 89)
2. Implement rate limiting for large files (line 120)

**Minor Issues 🟢:**
1. Add more detailed logging (line 45)
2. Consider using async CSV reader for better performance

**Positive:**
- Excellent error handling
- Good separation of concerns
- Comprehensive type hints

Would you like me to fix these issues?
```

**Step 5: Run Tests**

```bash
> /test --coverage
```

**Expected Output:**

```
🧪 Running test suite...

tests/unit/test_import.py ............. [ 35%]
tests/unit/test_csv_parser.py ........ [ 55%]
tests/unit/test_validator.py ......... [ 75%]
tests/integration/test_bulk_import.py  [100%]

============================== 87 passed in 8.2s ==============================

Coverage: 94% (exceeds 85%)

Missing coverage: 
- src/services/import_service.py: line 156 (edge case)
- src/api/import.py: line 234 (error scenario)

Would you like me to add tests for uncovered lines?
```

**Step 6: Deploy to Staging**

```bash
> /deploy staging
```

**Expected Output:**

```
🚀 Deploying to staging...

Pre-deployment checks:
✅ All tests passing
✅ Security scan passed
✅ Linting passed

Building Docker images...
✅ product-service:latest (124 MB)
✅ order-service:latest (156 MB)
✅ user-service:latest (98 MB)
✅ api-gateway:latest (67 MB)

Pushing to registry...
✅ All images pushed

Deploying to Kubernetes...
✅ product-service deployed (3 replicas)
✅ order-service deployed (3 replicas)
✅ user-service deployed (3 replicas)
✅ api-gateway deployed (2 replicas)

Health checks:
✅ All services healthy

Staging URL: https://staging.ecommerce.com

Deployment complete in 2m 34s

Would you like to run smoke tests?
```

**Step 7: Production Deployment (with Canary)**

```bash
> /deploy production --canary
```

**Expected Output:**

```
🚀 Production deployment with canary strategy...

⚠️ Production deployment detected

Canary configuration:
- Initial traffic: 10%
- Evaluation period: 5 minutes
- Success criteria: 99.9% success rate

Deploying canary...
✅ Canary deployed (1 replica)

Monitoring canary...
📊 Success rate: 100% (after 2 minutes)
📊 Latency: 45ms (acceptable)
📊 Error rate: 0%

Promoting canary...
✅ Full deployment complete

Production URL: https://api.ecommerce.com

Rollback available for 24 hours
```

---

## Part 5: Monitoring and Observability

### Integrated Monitoring Setup

Create `kubernetes/monitoring/prometheus-config.yaml`:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: prometheus-config
  namespace: monitoring
data:
  prometheus.yml: |
    global:
      scrape_interval: 15s
      evaluation_interval: 15s
    
    scrape_configs:
    - job_name: 'kubernetes-pods'
      kubernetes_sd_configs:
      - role: pod
      relabel_configs:
      - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
        action: keep
        regex: true
      - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
        action: replace
        target_label: __metrics_path__
        regex: (.+)
      - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
        action: replace
        regex: ([^:]+)(?::\d+)?;(\d+)
        replacement: $1:$2
        target_label: __address__
    
    - job_name: 'services'
      static_configs:
      - targets:
        - 'product-service.ecommerce.svc.cluster.local:8000'
        - 'order-service.ecommerce.svc.cluster.local:8000'
        - 'user-service.ecommerce.svc.cluster.local:8000'
        - 'api-gateway.ecommerce.svc.cluster.local:8000'
```

### Adding Metrics to Services

```python
# shared/utils/metrics.py
"""
Prometheus metrics for all services.
"""

from prometheus_client import Counter, Histogram, Gauge, generate_latest
from fastapi import Response
import time
from typing import Callable
import functools

# Define metrics
http_requests_total = Counter(
    'http_requests_total',
    'Total HTTP requests',
    ['method', 'endpoint', 'status']
)

http_request_duration = Histogram(
    'http_request_duration_seconds',
    'HTTP request duration',
    ['method', 'endpoint']
)

active_connections = Gauge(
    'active_connections',
    'Active connections'
)

business_metrics = {
    'orders_created': Counter('orders_created_total', 'Total orders created'),
    'products_sold': Counter('products_sold_total', 'Total products sold'),
    'revenue': Counter('revenue_total', 'Total revenue', ['currency'])
}

def track_metrics(func: Callable):
    """Decorator to track function metrics."""
    @functools.wraps(func)
    async def wrapper(*args, **kwargs):
        start_time = time.time()
        
        try:
            result = await func(*args, **kwargs)
            duration = time.time() - start_time
            
            # Record metrics
            http_request_duration.labels(
                method=func.__name__,
                endpoint=func.__module__
            ).observe(duration)
            
            return result
        except Exception as e:
            duration = time.time() - start_time
            http_requests_total.labels(
                method=func.__name__,
                endpoint=func.__module__,
                status='error'
            ).inc()
            raise
    
    return wrapper

async def metrics_endpoint():
    """Metrics endpoint for Prometheus."""
    return Response(
        content=generate_latest(),
        media_type="text/plain"
    )
```

---

## Part 6: Complete Development Environment in Action

### Putting It All Together

Here's a complete video-style walkthrough of a development session:

```bash
# 9:00 AM - Start the day
cd ecommerce-microservices
code .  # Open VS Code

# VS Code opens with the project
# Terminal 1: Start Claude Code
claude --plan

# 9:05 AM - Plan today's work
> /plan "Add product reviews feature with rating system"

# Claude creates detailed plan with 5 subagents
# Estimated: 3 hours, 850 lines

# 9:10 AM - Start implementation
> yes

# Terminal 2: Monitor progress
watch -n 5 'claude /subagents status'

# 9:45 AM - Code review of completed components
> /review src/services/review_service.py

# 10:00 AM - Run tests
> /test --coverage

# 10:15 AM - Deploy to staging
> /deploy staging

# 10:30 AM - Smoke tests pass, ready for review

# 11:00 AM - Team code review in VS Code
# Use GitLens to see changes
# Use Claude to explain complex sections

> /explain src/services/review_service.py:45-78

# 11:30 AM - Merge to main
git add .
git commit -m "Add product reviews feature"
git push origin main

# CI/CD automatically runs:
# - Tests
# - Security scan
# - Build images
# - Deploy to staging

# 12:00 PM - Deploy to production
> /deploy production --canary

# 12:15 PM - Monitor production metrics
> /metrics show

# Production metrics:
# - Success rate: 99.98%
# - P99 latency: 125ms
# - Error rate: 0.02%
# - Orders/min: 1,234

# 1:00 PM - Feature complete, day done!
```

---

## Summary: The Complete Claude Code + VS Code Experience

```mermaid
```

![- Error rate: 0.02%](images/diagram_03_summary-the-complete-claude-code--vs-code-exp-d6b5.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/claude-code-mastery---from-terminal-to-ide-complete-vs-code-integration--real-world-project-workflow/diagram_03_summary-the-complete-claude-code--vs-code-exp-d6b5.md)


### Key Takeaways


![Table](images/table_01_key-takeaways.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/claude-code-mastery---from-terminal-to-ide-complete-vs-code-integration--real-world-project-workflow/table_01_key-takeaways.md)



### Final Command Reference

```bash
# Claude Commands
claude --plan                    # Start with plan mode
/plan "feature"                  # Create detailed plan
/subagents status                # Monitor parallel agents
/review <file>                   # Code review
/test --coverage                 # Run tests with coverage
/deploy <env> [--canary]         # Deploy with canary
/metrics show                    # Show production metrics
/compact                         # Optimize context

# VS Code Tasks
Ctrl+Shift+P → Tasks: Run Task
  → "Claude: Start Session"
  → "Claude: Code Review"
  → "Claude: Generate Tests"
  → "Run All Tests"
  → "Start Development Server"

# Docker/Kubernetes
docker-compose up -d             # Start local environment
kubectl apply -f kubernetes/     # Deploy to K8s
kubectl get pods -n ecommerce    # Check status

# CI/CD
git push origin main              # Trigger CI/CD pipeline
git tag v1.0.0                    # Trigger production deploy
```

---

## Conclusion: Your AI-Powered Development Future

Throughout this four-part series, we've explored all 12 features of Claude Code:

1. **The Memory & Control Layer**: CLAUDE.md, Permissions, Plan Mode, Checkpoints
2. **The Extension & Integration Framework**: Skills, Hooks, MCP, Plugins
3. **The Advanced Workflow Engine**: Context Management, Slash Commands, Compaction, Subagents
4. **Complete VS Code Integration**: Real-world project workflow

These features combine to create a development experience that is:

- **Safer**: With permissions, checkpoints, and plan mode
- **Smarter**: With context management and compaction
- **Faster**: With parallel subagents and slash commands
- **More Powerful**: With skills, hooks, MCP, and plugins
- **Fully Integrated**: Working seamlessly with VS Code and your existing tools

The result is an AI-powered development platform that doesn't just answer questions—it becomes a true partner in building production-ready software.

```mermaid
```

![Safer](images/diagram_04_the-result-is-an-ai-powered-development-platform-t-a508.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/claude-code-mastery---from-terminal-to-ide-complete-vs-code-integration--real-world-project-workflow/diagram_04_the-result-is-an-ai-powered-development-platform-t-a508.md)

---

Coming soon! Want it sooner? Let me know with a clap or comment below


*� Questions? Drop a response - I read and reply to every comment.*  
*📌 Save this story to your reading list - it helps other engineers discover it.*  
**🔗 Follow me →**

- **[Medium](mvineetsharma.medium.com)** - mvineetsharma.medium.com
- **[LinkedIn](www.linkedin.com/in/vineet-sharma-architect)** -  [www.linkedin.com/in/vineet-sharma-architect](http://www.linkedin.com/in/vineet-sharma-architect)

*In-depth .NET, Node.js, Python, Cloud Architecture, and System Design. New articles weekly*