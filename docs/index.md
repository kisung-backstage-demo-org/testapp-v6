# testapp-v6 Documentation

Welcome to the **testapp-v6** documentation! This Spring Boot microservice is designed with cloud-native best practices and automated governance.

## 🎯 Overview

testapp-v6 is a production-ready REST API service that demonstrates:

- ✅ **Spring Boot 3.2** with Java 17
- ✅ **GitOps deployment** via ArgoCD
- ✅ **Container security** with Trivy scanning
- ✅ **Kubernetes native** with health probes
- ✅ **CI/CD automation** via GitHub Actions
- ✅ **Developer Portal** integration with Backstage

## 📊 Service Status

| Property | Value |
|----------|-------|
| **Service Name** | testapp-v6 |
| **Description** | test |
| **Language** | Java 17 |
| **Framework** | Spring Boot 3.2 |
| **Container Registry** | GitHub Container Registry (GHCR) |
| **Deployment** | Kubernetes via ArgoCD |
| **Owner Team** | platform |
| **Target Cluster** | demo |
| **Namespace** | default |

## 🚀 Quick Links

- [GitHub Repository](https://github.com/kisung-backstage-demo-org/testapp-v6)
- [API Reference](api-reference.md)
- [Deployment Guide](deployment.md)
- [Security Overview](security.md)

## 📚 Documentation Sections

### For Developers
- [Getting Started](getting-started.md) - Set up your local development environment
- [Local Development](local-development.md) - Run the service locally
- [API Reference](api-reference.md) - REST API endpoints and examples

### For Architects
- [Architecture Overview](architecture.md) - System design and components
- [Tech Stack](tech-stack.md) - Technologies and dependencies

### For DevOps
- [Kubernetes Deployment](deployment.md) - Deploy to EKS clusters
- [ArgoCD GitOps](argocd.md) - Continuous deployment setup
- [Monitoring](monitoring.md) - Observability and alerts

### For Security Teams
- [Security Overview](security.md) - Security posture and compliance
- [Trivy Scanning](trivy-scan.md) - Automated vulnerability scanning

## 🆘 Need Help?

Check out our [Troubleshooting Guide](troubleshooting.md) or contact the Platform Team on Slack.

---

**Last Updated**: {{ now().strftime('%Y-%m-%d') }}  
**Version**: 1.0.0  
**Status**: Production Ready ✅
