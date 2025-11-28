# testapp-v6

test

## 📋 Overview

- **Type**: Spring Boot REST API Service
- **Owner**: Team platform
- **Target Cluster**: 
- **Namespace**: default

## 🚀 Quick Start

### Local Development

```bash
# Build
mvn clean package

# Run
mvn spring-boot:run

# Test
curl http://localhost:8080/api/hello
```

### Docker

```bash
# Build image
docker build -t testapp-v6:latest .

# Run container
docker run -p 8080:8080 testapp-v6:latest
```

## 📦 Deployment

### ArgoCD

This service is automatically deployed via ArgoCD:

1. Code is pushed to `main` branch
2. GitHub Actions builds Docker image
3. Image is pushed to GHCR
4. Kubernetes manifests are updated
5. ArgoCD syncs automatically

### Manual Deployment

```bash
# Apply ArgoCD Application
kubectl apply -f argocd/application.yaml

# Check deployment status
kubectl get deployment testapp-v6 -n default
```

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────┐
│ GitHub Repository                                │
│  ├─ src/                 (Java source code)      │
│  ├─ k8s/                 (K8s manifests)         │
│  ├─ argocd/              (ArgoCD app)            │
│  └─ .github/workflows/   (CI/CD)                 │
└─────────────────────────────────────────────────┘
                    │
                    ▼
┌─────────────────────────────────────────────────┐
│ GitHub Actions                                   │
│  ├─ Build with Maven                             │
│  ├─ Run tests                                    │
│  ├─ Build Docker image                           │
│  └─ Push to GHCR                                 │
└─────────────────────────────────────────────────┘
                    │
                    ▼
┌─────────────────────────────────────────────────┐
│ ArgoCD                                           │
│  ├─ Monitors Git repository                      │
│  ├─ Syncs K8s manifests                          │
│  └─ Deploys to EKS                               │
└─────────────────────────────────────────────────┘
                    │
                    ▼
┌─────────────────────────────────────────────────┐
│ EKS Cluster:            │
│  └─ Namespace: default          │
│     ├─ Deployment (2 pods) │
│     ├─ Service (ClusterIP)                       │
│     └─ Ingress                                   │
└─────────────────────────────────────────────────┘
```

## 📊 API Endpoints

- `GET /api/hello` - Hello endpoint
- `GET /actuator/health` - Health check
- `GET /actuator/info` - Service information
- `GET /actuator/metrics` - Metrics

## 🔧 Configuration

See `src/main/resources/application.yml` for configuration.

## 📝 License

Copyright © 2025 Team platform
