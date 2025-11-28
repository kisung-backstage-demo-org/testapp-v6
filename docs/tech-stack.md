# Tech Stack

Complete list of technologies and tools used in testapp-v6.

## Core Technologies

### Backend Framework

| Technology | Version | Purpose |
|------------|---------|---------|
| **Spring Boot** | 3.2.0 | Application framework |
| **Java** | 17 (LTS) | Programming language |
| **Maven** | 3.8+ | Build tool |

### Container & Orchestration

| Technology | Version | Purpose |
|------------|---------|---------|
| **Docker** | 24+ | Containerization |
| **Kubernetes** | 1.32+ | Container orchestration |
| **ArgoCD** | Latest | GitOps deployment |

## Development Tools

### IDE Support

- ✅ IntelliJ IDEA (recommended)
- ✅ VS Code with Java Extension Pack
- ✅ Eclipse

### Testing

| Tool | Purpose |
|------|---------|
| JUnit 5 | Unit testing |
| Spring Test | Integration testing |
| MockMvc | REST API testing |
| Testcontainers | Container testing |

## CI/CD Stack

### GitHub Actions Workflows

```yaml
workflows/
├── ci-cd.yaml       # Main CI/CD pipeline
└── trivy-scan.yml   # Security scanning
```

**Technologies:**
- GitHub Actions
- Maven
- Docker BuildX
- Trivy Scanner

### Container Registry

**GitHub Container Registry (GHCR)**
- Location: `ghcr.io/kisung-backstage-demo-org/testapp-v6`
- Authentication: GitHub token
- Public/Private: Private

## Security Stack

### Vulnerability Scanning

| Tool | Purpose | Frequency |
|------|---------|-----------|
| **Trivy** | Container scanning | Every push + weekly |
| **Trivy** | Config scanning | Every push |
| **Dependabot** | Dependency updates | Daily |

### Governance

- **Backstage Governance Plugin**: Real-time security dashboard
- **GitHub Security Advisories**: CVE alerts
- **GHCR Scanning**: Built-in container scanning

## Infrastructure

### Cloud Provider

**AWS (Amazon Web Services)**
- Region: `ap-northeast-2` (Seoul)
- EKS Cluster: `demo`
- VPC: Shared (`vpc-026395ad36c04f600`)

### Kubernetes Resources

```yaml
Resources:
  CPU:
    Request: 250m
    Limit: 500m
  Memory:
    Request: 512Mi
    Limit: 1Gi
  Replicas: 2
```

## Observability Stack

### Monitoring (Current)

- Spring Boot Actuator
- Kubernetes health probes

### Monitoring (Planned)

- Prometheus metrics
- Grafana dashboards
- AlertManager alerts

### Logging (Planned)

- ELK Stack (Elasticsearch, Logstash, Kibana)
- CloudWatch Logs
- Structured JSON logging

### Tracing (Planned)

- OpenTelemetry
- Jaeger
- Distributed tracing

## Developer Portal

### Backstage Integration

**Features:**
- 📦 Service Catalog
- 📊 Governance Dashboard
- 🚀 Scaffolder Templates
- 📚 TechDocs (this documentation!)
- 🔍 Search
- 🎯 Kubernetes Plugin

## Dependency Matrix

### Spring Boot Dependencies

```xml
<dependencies>
  <!-- Core -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
  </dependency>
  
  <!-- Actuator -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
  </dependency>
  
  <!-- Testing -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
  </dependency>
</dependencies>
```

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2025-11-27 | Initial release |

## Technology Radar

```
Adopt:
- Spring Boot 3.2
- Kubernetes
- ArgoCD
- Trivy

Trial:
- GraalVM Native Image
- Service Mesh (Istio)

Assess:
- Dapr
- WebAssembly

Hold:
- Monolithic architecture
- Manual deployments
```

---

**Want to contribute?** Check our [GitHub Repository](https://github.com/kisung-backstage-demo-org/testapp-v6)!
