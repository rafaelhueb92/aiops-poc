# Kubernetes Manifests for Kube-News

This folder contains a single Kubernetes manifest file for deploying the kube-news application with PostgreSQL database.

## Files Overview

- **manifest.yaml** - Single file containing all Kubernetes resources:
  - Namespace: `kube-news`
  - Secret: Database credentials
  - Services: PostgreSQL (ClusterIP) and App (LoadBalancer)
  - Deployments: PostgreSQL 15 and Node.js application

## Deployment Instructions

### 1. Build and Push Docker Image

First, build the Docker image:
```bash
docker build -t kube-news:latest ./src
```

If using a container registry:
```bash
docker build -t your-registry/kube-news:latest ./src
docker push your-registry/kube-news:latest
```

Then update the image reference in `manifest.yaml` if using a registry.

### 2. Deploy to Kubernetes

Apply the single manifest:
```bash
kubectl apply -f k8s/manifest.yaml
```

### 3. Verify Deployment

```bash
# Check namespace
kubectl get namespaces

# Check all resources
kubectl get all -n kube-news

# Check pods
kubectl get pods -n kube-news

# Check services
kubectl get svc -n kube-news

# View pod logs
kubectl logs -n kube-news <pod-name>
```

### 4. Access the Application

Get the external IP of the LoadBalancer service:
```bash
kubectl get svc -n kube-news kube-news-service
```

Once the external IP is assigned, access the application at:
```
http://<EXTERNAL-IP>
```

## Configuration Notes

### Secrets
- Database credentials are stored in the Secret resource in `manifest.yaml`
- Modify credentials before deploying to production
- Consider using a secrets management solution (e.g., Sealed Secrets, External Secrets)

### Storage
- PostgreSQL uses emptyDir volume (non-persistent)
- Data is lost when the pod restarts
- For persistent storage, add a PersistentVolumeClaim and update the postgres deployment volume reference

### Application
- App deployment has 2 replicas for high availability
- Includes liveness and readiness probes
- Resource limits: 512Mi memory, 500m CPU (adjust based on load)

### Scaling

Scale the application:
```bash
kubectl scale deployment kube-news-app -n kube-news --replicas=3
```

## Cleanup

To remove all resources:
```bash
kubectl delete namespace kube-news
```

This will delete all resources in the namespace.

## Production Considerations

1. **Secrets Management**: Use Kubernetes Secrets operators or external secrets management
2. **Storage**: Use persistent storage (PersistentVolumeClaim with appropriate storage class)
3. **Database**: Consider managed database services for production
4. **Networking**: Set up Network Policies for pod-to-pod communication
5. **Ingress**: Use Ingress instead of LoadBalancer for better routing
6. **Monitoring**: Set up Prometheus/Grafana for metrics
7. **Logging**: Configure centralized logging (ELK, Loki, etc.)
8. **Image Registry**: Use a private registry for production images
9. **Resource Quotas**: Set resource quotas per namespace
10. **RBAC**: Configure proper role-based access control
