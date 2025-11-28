# Health Endpoints

Detailed documentation for testapp-v6 health and monitoring endpoints.

## Spring Boot Actuator

testapp-v6 uses Spring Boot Actuator for production-ready operational features.

## Available Endpoints

### Health Check (Main)

```http
GET /actuator/health
```

**Purpose**: Overall application health status

**Response (200 OK)**:
```json
{
  "status": "UP",
  "components": {
    "diskSpace": {
      "status": "UP",
      "details": {
        "total": 250790436864,
        "free": 100000000000,
        "threshold": 10485760,
        "path": "/app",
        "exists": true
      }
    },
    "ping": {
      "status": "UP"
    }
  }
}
```

### Liveness Probe

```http
GET /actuator/health/liveness
```

**Purpose**: Check if application is running  
**Used by**: Kubernetes liveness probe  
**Response**: `{"status":"UP"}` or `{"status":"DOWN"}`

**When DOWN**: Kubernetes kills and restarts the pod

### Readiness Probe

```http
GET /actuator/health/readiness
```

**Purpose**: Check if application is ready to serve traffic  
**Used by**: Kubernetes readiness probe  
**Response**: `{"status":"UP"}` or `{"status":"DOWN"}`

**When DOWN**: Kubernetes removes pod from service load balancer

## Health Indicators

### Disk Space

Checks available disk space.

**Healthy When:**
- Free space > 10MB

**Example:**
```json
{
  "diskSpace": {
    "status": "UP",
    "details": {
      "total": 250790436864,
      "free": 100000000000,
      "threshold": 10485760
    }
  }
}
```

### Ping

Simple ping check to verify application responsiveness.

**Always Returns:** `UP` (unless application is completely unresponsive)

### Database (Future)

```json
{
  "db": {
    "status": "UP",
    "details": {
      "database": "PostgreSQL",
      "validationQuery": "isValid()"
    }
  }
}
```

## Metrics Endpoint

```http
GET /actuator/metrics
```

**Response**: List of available metrics

```json
{
  "names": [
    "jvm.memory.used",
    "jvm.memory.max",
    "http.server.requests",
    "process.cpu.usage",
    "system.cpu.usage",
    "jvm.threads.live"
  ]
}
```

### Specific Metric

```http
GET /actuator/metrics/{metric-name}
```

**Example:**
```bash
curl http://localhost:/actuator/metrics/jvm.memory.used
```

**Response:**
```json
{
  "name": "jvm.memory.used",
  "measurements": [
    {
      "statistic": "VALUE",
      "value": 268435456
    }
  ],
  "availableTags": [
    {
      "tag": "area",
      "values": ["heap", "nonheap"]
    }
  ]
}
```

## Info Endpoint

```http
GET /actuator/info
```

**Purpose**: Application build and version information

**Response:**
```json
{
  "app": {
    "name": "testapp-v6",
    "version": "1.0.0",
    "encoding": "UTF-8",
    "java": {
      "version": "17.0.8"
    }
  },
  "build": {
    "artifact": "testapp-v6",
    "name": "testapp-v6",
    "time": "2025-11-27T12:00:00.000Z",
    "version": "1.0.0",
    "group": "com.example"
  }
}
```

## Prometheus Endpoint (Planned)

```http
GET /actuator/prometheus
```

**Purpose**: Export metrics in Prometheus format

**Response:**
```
# HELP jvm_memory_used_bytes Memory used
# TYPE jvm_memory_used_bytes gauge
jvm_memory_used_bytes{area="heap"} 268435456.0
jvm_memory_used_bytes{area="nonheap"} 67108864.0
```

## Custom Health Indicators

### Adding Custom Checks

```java
@Component
public class CustomHealthIndicator implements HealthIndicator {
    @Override
    public Health health() {
        // Your custom health check logic
        boolean isHealthy = checkSomething();
        
        if (isHealthy) {
            return Health.up()
                .withDetail("custom", "Everything is fine")
                .build();
        } else {
            return Health.down()
                .withDetail("error", "Something is wrong")
                .build();
        }
    }
}
```

## Configuration

### application.yml

```yaml
management:
  endpoints:
    web:
      exposure:
        include: health,info,metrics
      base-path: /actuator
  endpoint:
    health:
      show-details: always
      probes:
        enabled: true
  health:
    diskspace:
      enabled: true
      threshold: 10485760  # 10MB
```

## Security Considerations

### Expose Selectively

**Public Endpoints:**
- `/actuator/health` (limited details)
- `/actuator/info`

**Internal Only:**
- `/actuator/metrics`
- `/actuator/env`
- `/actuator/loggers`

### Authentication (Planned)

```yaml
management:
  endpoints:
    web:
      exposure:
        include: "*"
  endpoint:
    health:
      show-details: when-authorized
```

## Monitoring Integration

### Kubernetes Probes

```yaml
# Using health endpoints
livenessProbe:
  httpGet:
    path: /actuator/health/liveness
    port: 8080
  initialDelaySeconds: 30
  periodSeconds: 10

readinessProbe:
  httpGet:
    path: /actuator/health/readiness
    port: 8080
  initialDelaySeconds: 20
  periodSeconds: 5
```

### Prometheus Scraping (Planned)

```yaml
# Service annotations
metadata:
  annotations:
    prometheus.io/scrape: "true"
    prometheus.io/path: "/actuator/prometheus"
    prometheus.io/port: "8080"
```

## Testing Health Endpoints

### Local Testing

```bash
# Health check
curl -v http://localhost:/actuator/health

# Liveness
curl -v http://localhost:/actuator/health/liveness

# Readiness
curl -v http://localhost:/actuator/health/readiness

# All metrics
curl -v http://localhost:/actuator/metrics
```

### Kubernetes Testing

```bash
# Via service
kubectl run curl-test --image=curlimages/curl -i --rm --restart=Never -- \
  curl http://testapp-v6.default.svc.cluster.local:/actuator/health

# Via port-forward
kubectl port-forward svc/testapp-v6 8080: -n default
curl http://localhost:/actuator/health
```

## Health Status Codes

| HTTP Code | Status | Meaning |
|-----------|--------|---------|
| **200** | UP | All components healthy |
| **503** | DOWN | One or more components unhealthy |
| **200** | UNKNOWN | Health status cannot be determined |

## Best Practices

✅ **Always implement health checks**  
✅ **Separate liveness from readiness**  
✅ **Include downstream dependencies in health**  
✅ **Set appropriate timeouts**  
✅ **Monitor health endpoint response times**  
✅ **Log health check failures**  

---

**Related**: [Monitoring Guide](monitoring.md)
