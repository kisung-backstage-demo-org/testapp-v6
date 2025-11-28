# Troubleshooting Guide

Common issues and solutions for testapp-v6.

## Quick Diagnostics

```bash
# Check everything at once
kubectl get deployment,pod,service,ingress -l app=testapp-v6 -n default
kubectl logs -l app=testapp-v6 -n default --tail=50
kubectl describe pod -l app=testapp-v6 -n default
```

## Common Issues

### 1. Pod Not Starting

**Symptoms:**
- Pod status: `CrashLoopBackOff`, `ImagePullBackOff`, or `Error`

**Diagnosis:**
```bash
# Check pod status
kubectl get pods -l app=testapp-v6 -n default

# View pod events
kubectl describe pod <pod-name> -n default

# Check logs
kubectl logs <pod-name> -n default
```

**Solutions:**

**ImagePullBackOff:**
```bash
# Verify image exists
docker pull ghcr.io/kisung-backstage-demo-org/testapp-v6:latest

# Check imagePullSecrets (if private)
kubectl get secrets -n default

# Update deployment with correct image
kubectl set image deployment/testapp-v6 \
  testapp-v6=ghcr.io/kisung-backstage-demo-org/testapp-v6:latest
```

**CrashLoopBackOff:**
```bash
# Check application logs
kubectl logs <pod-name> -n default --previous

# Common causes:
# - Missing environment variables
# - Port already in use
# - Insufficient memory
# - Application startup failure
```

### 2. Service Not Accessible

**Symptoms:**
- Cannot reach service endpoints
- Connection timeout

**Diagnosis:**
```bash
# Check service
kubectl get service testapp-v6 -n default

# Check endpoints
kubectl get endpoints testapp-v6 -n default

# Check if pods are ready
kubectl get pods -l app=testapp-v6 -n default
```

**Solutions:**

**No Endpoints:**
```bash
# Pods not ready - check readiness probe
kubectl describe pod <pod-name> -n default

# Fix: Ensure /actuator/health/readiness returns 200
```

**Service Port Mismatch:**
```bash
# Verify service configuration
kubectl get service testapp-v6 -n default -o yaml

# Should match container port (8080)
```

### 3. High Memory Usage

**Symptoms:**
- OOMKilled events
- Pod restarts

**Diagnosis:**
```bash
# Check memory usage
kubectl top pod -l app=testapp-v6 -n default

# Check events for OOM
kubectl get events -n default | grep OOM
```

**Solutions:**

**Increase Memory Limit:**
```yaml
# k8s/deployment.yaml
resources:
  requests:
    memory: "1Gi"      # Increased from 512Mi
  limits:
    memory: "2Gi"      # Increased from 1Gi
```

**Optimize JVM:**
```yaml
env:
- name: JAVA_OPTS
  value: "-Xms512m -Xmx1024m -XX:MaxMetaspaceSize=256m"
```

### 4. GitHub Actions Failing

**Symptoms:**
- CI/CD pipeline fails
- Image not built

**Check Logs:**
```
https://github.com/kisung-backstage-demo-org/testapp-v6/actions
→ Failed run → View logs
```

**Common Issues:**

**Maven Build Failure:**
```bash
# Locally reproduce
mvn clean package

# Check for test failures
mvn test
```

**Docker Build Failure:**
```bash
# Build locally
docker build -t testapp-v6:test .

# Check Dockerfile syntax
docker build --no-cache -t testapp-v6:test .
```

**GHCR Push Failure:**
```bash
# Check GITHUB_TOKEN permissions
# Must have packages:write permission
```

### 5. ArgoCD Not Syncing

**Symptoms:**
- Application shows OutOfSync
- Changes not deployed

**Diagnosis:**
```bash
# Check ArgoCD application
kubectl get application testapp-v6 -n argocd

# Describe for errors
kubectl describe application testapp-v6 -n argocd

# ArgoCD CLI
argocd app get testapp-v6
```

**Solutions:**

**Manual Sync:**
```bash
argocd app sync testapp-v6

# Force sync
argocd app sync testapp-v6 --force
```

**Check Repository Access:**
```bash
# Verify GitHub repository is accessible
git clone https://github.com/kisung-backstage-demo-org/testapp-v6.git
```

### 6. Health Check Failures

**Symptoms:**
- Liveness/Readiness probes failing
- Pod restarts frequently

**Diagnosis:**
```bash
# Test health endpoint
kubectl exec -it <pod-name> -n default -- \
  curl http://localhost:/actuator/health

# Check probe configuration
kubectl get pod <pod-name> -n default -o yaml | grep -A 10 "livenessProbe"
```

**Solutions:**

**Increase Initial Delay:**
```yaml
livenessProbe:
  initialDelaySeconds: 60  # Increased from 30
```

**Increase Timeout:**
```yaml
readinessProbe:
  timeoutSeconds: 5  # Default is 1
```

## Debugging Tools

### Port Forwarding

```bash
# Forward service port
kubectl port-forward svc/testapp-v6 8080: -n default

# Forward specific pod
kubectl port-forward <pod-name> 8080: -n default
```

### Execute Commands in Pod

```bash
# Interactive shell
kubectl exec -it <pod-name> -n default -- /bin/sh

# Run command
kubectl exec <pod-name> -n default -- curl localhost:/actuator/health
```

### Debug Container

```bash
# Create debug pod
kubectl run debug --image=busybox -i --rm --restart=Never -- sh

# Inside debug pod
wget -O- http://testapp-v6.default.svc.cluster.local:/api/hello
```

## Performance Issues

### Slow Response Time

**Check:**
```bash
# JVM metrics
curl http://localhost:/actuator/metrics/jvm.memory.used

# Thread count
curl http://localhost:/actuator/metrics/jvm.threads.live

# GC activity
curl http://localhost:/actuator/metrics/jvm.gc.pause
```

**Solutions:**
- Increase CPU limits
- Optimize code (profiling)
- Add caching layer
- Scale horizontally

### High CPU Usage

```bash
# Check CPU usage
kubectl top pod -l app=testapp-v6 -n default

# Thread dump
kubectl exec <pod-name> -n default -- jstack 1
```

## Logs Analysis

### Common Log Patterns

**Successful Request:**
```
2025-11-27 12:00:00.123  INFO   GET /api/hello - 200 OK (45ms)
```

**Error:**
```
2025-11-27 12:00:00.456  ERROR  NullPointerException at ...
```

**Health Check:**
```
2025-11-27 12:00:00.789  DEBUG  Health check: UP
```

### Log Aggregation Commands

```bash
# Errors only
kubectl logs -l app=testapp-v6 -n default | grep ERROR

# Last hour
kubectl logs -l app=testapp-v6 -n default --since=1h

# Multiple pods
kubectl logs -l app=testapp-v6 -n default --all-containers=true
```

## Emergency Procedures

### Service Down

```bash
# 1. Check pod status
kubectl get pods -l app=testapp-v6 -n default

# 2. Restart deployment
kubectl rollout restart deployment/testapp-v6 -n default

# 3. If still failing, rollback
kubectl rollout undo deployment/testapp-v6 -n default

# 4. Check ArgoCD for automatic recovery
kubectl get application testapp-v6 -n argocd
```

### Database Connection Issues (Future)

```bash
# Check database connectivity
kubectl exec <pod-name> -n default -- \
  curl postgres.default.svc.cluster.local:5432

# Verify credentials
kubectl get secret db-credentials -n default -o yaml
```

## Getting Help

### Support Channels

1. **Backstage**: Check [Troubleshooting docs](http://localhost:7007/catalog/default/component/testapp-v6/docs/troubleshooting)
2. **Slack**: #platform-support
3. **GitHub Issues**: [Create Issue](https://github.com/kisung-backstage-demo-org/testapp-v6/issues/new)
4. **On-call**: PagerDuty escalation

### Escalation Path

1. Team Lead
2. Platform Engineering Team
3. Infrastructure Team
4. Security Team (for security issues)

---

**Still stuck?** Contact Platform Team on Slack!
