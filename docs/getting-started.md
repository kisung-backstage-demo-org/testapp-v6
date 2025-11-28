# Getting Started

This guide will help you get testapp-v6 running on your local machine for development and testing.

## Prerequisites

Before you begin, ensure you have the following installed:

- ☕ **Java 17** or higher
- 🏗️ **Maven 3.8+**
- 🐳 **Docker** (for containerized testing)
- 🔧 **kubectl** (for Kubernetes deployment)
- 🔑 **AWS CLI** (configured with credentials)

## Quick Start

### 1. Clone the Repository

```bash
git clone https://github.com/kisung-backstage-demo-org/testapp-v6.git
cd testapp-v6
```

### 2. Build the Application

```bash
# Build with Maven
mvn clean package

# Run tests
mvn test
```

### 3. Run Locally

```bash
# Run Spring Boot application
mvn spring-boot:run

# Or run the JAR directly
java -jar target/testapp-v6-1.0.0.jar
```

The service will start on port ****.

### 4. Test the API

```bash
# Health check
curl http://localhost:/actuator/health

# Hello endpoint
curl http://localhost:/api/hello?name=Developer

# Expected response
"Hello, Developer!"
```

## Docker Development

### Build Docker Image

```bash
docker build -t testapp-v6:local .
```

### Run in Docker

```bash
docker run -p : testapp-v6:local
```

### Test Container

```bash
# Health check
curl http://localhost:/actuator/health

# API endpoint
curl http://localhost:/api/hello
```

## Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `SERVER_PORT` | HTTP port |  |
| `SPRING_PROFILES_ACTIVE` | Active profile | default |
| `LOG_LEVEL` | Logging level | INFO |

## IDE Setup

### IntelliJ IDEA

1. Open the project
2. Maven will auto-import dependencies
3. Run configuration: `Application.java` → Run

### VS Code

1. Install Java Extension Pack
2. Open project
3. Run → Start Debugging

## Next Steps

- 📖 Read the [Architecture Overview](architecture.md)
- 🔌 Explore the [API Reference](api-reference.md)
- 🚀 Learn about [Kubernetes Deployment](deployment.md)
- 🔒 Review [Security Practices](security.md)

## Troubleshooting

If you encounter issues:

1. Check [Troubleshooting Guide](troubleshooting.md)
2. Review GitHub Actions logs
3. Contact Platform Team on Slack

---

**Happy Coding!** 🎉
