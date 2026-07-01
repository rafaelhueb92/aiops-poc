# GitHub Actions Workflow Setup - 2026-07-01

**Date:** 2026-07-01  
**Duration:** Workflow Creation  
**Severity:** Informational  
**Status:** Completed

---

## Executive Summary

Successfully created GitHub Actions CI/CD workflow for the kube-news application. Workflow builds Docker image from `./src/` and applies Kubernetes manifests from `./k8s/`. No errors encountered during setup.

---

## Workflow Details

### Created Artifact
- **File:** `.github/workflows/build-and-deploy.yml`
- **Purpose:** Automated build and deployment pipeline
- **Triggers:** Push to main/master branches + manual dispatch

### Workflow Jobs

1. **build-and-push**
   - Builds Docker image from `./src/Dockerfile`
   - Pushes to Docker Hub (`rafahueb/kube-news:latest`)
   - Uses GitHub Actions cache for faster builds

2. **deploy**
   - Applies Kubernetes manifests from `./k8s/manifest.yaml`
   - Verifies deployment rollout status
   - Depends on successful build completion

---

## Required Configuration

### GitHub Secrets to Configure
```
DOCKERHUB_USERNAME  - Docker Hub username
DOCKERHUB_TOKEN     - Docker Hub access token
KUBE_CONFIG         - Base64 encoded kubeconfig file
```

### Potential Issues & Solutions

#### Issue 1: Docker Hub Authentication Failure
**Cause:** Invalid DOCKERHUB_USERNAME or DOCKERHUB_TOKEN  
**Solution:** 
- Verify credentials in GitHub repo Settings > Secrets
- Generate new token at hub.docker.com/settings/security
- Ensure token has push permissions

#### Issue 2: Kubernetes Deployment Fails
**Cause:** Invalid KUBE_CONFIG or cluster unreachable  
**Solution:**
- Verify kubeconfig base64 encoding: `cat ~/.kube/config | base64`
- Check cluster connectivity from runner
- Ensure service account has necessary permissions

#### Issue 3: Image Pull Fails in Kubernetes
**Cause:** Image tag in manifest doesn't match pushed image  
**Cause:** ImagePullPolicy set to Always but image not yet available  
**Solution:**
- Manifest currently uses `rafahueb/kube-news:latest`
- Ensure registry credentials available in cluster if using private registry
- Consider adding imagePullSecrets to deployment if needed

#### Issue 4: Rollout Status Timeout
**Cause:** Deployment pods not reaching Ready state within 5 minutes  
**Solution:**
- Check pod logs: `kubectl logs -n kube-news deployment/kube-news-app`
- Verify resource requests/limits don't exceed cluster capacity
- Check liveness/readiness probe configuration in manifest.yaml
- Verify database (postgres) deployment is ready first

---

## Key Findings

### Design Decisions Made
1. **Two-job workflow** - Separation of concerns (build vs deploy)
2. **Docker Hub registry** - Public image storage
3. **Direct kubectl apply** - Simple deployment without GitOps tools
4. **Latest tag strategy** - Always pull latest image

### Workflow Capabilities
✓ Automatic builds on push to main/master  
✓ Manual trigger support via workflow_dispatch  
✓ Multi-stage Docker builds (dependencies separation)  
✓ GitHub Actions cache for faster builds  
✓ Rollout verification before declaring success  

---

## Recommendations

### Immediate Actions
1. ✅ Create GitHub secrets (DOCKERHUB_USERNAME, DOCKERHUB_TOKEN, KUBE_CONFIG)
2. ✅ Test workflow with manual dispatch before relying on push triggers
3. ✅ Monitor first few deployments for issues

### Future Improvements
1. Add image vulnerability scanning (Trivy, Snyk)
2. Implement canary deployment strategy
3. Add Slack/email notifications for deployment status
4. Add approval gates for production deployments
5. Implement semantic versioning with git tags
6. Add automated rollback on deployment failure
7. Separate staging and production environments

### Documentation Needs
- [ ] Update README with CI/CD pipeline documentation
- [ ] Create deployment troubleshooting guide
- [ ] Document rollback procedures
- [ ] Add disaster recovery plan

---

## Success Criteria Met

✓ Workflow file created at `.github/workflows/build-and-deploy.yml`  
✓ Docker build configured for `./src/Dockerfile`  
✓ Kubernetes manifest application configured for `./k8s/manifest.yaml`  
✓ Push triggers configured for main/master branches  
✓ Manual dispatch option enabled  
✓ Deployment verification step included  

---

## Next Steps

1. **Configure GitHub Secrets:**
   ```bash
   # Add to GitHub repo Settings > Secrets and variables > Actions
   DOCKERHUB_USERNAME=your_username
   DOCKERHUB_TOKEN=your_token
   KUBE_CONFIG=$(cat ~/.kube/config | base64)
   ```

2. **Test Workflow:**
   - Navigate to GitHub Actions tab
   - Click "Build and Deploy"
   - Select "Run workflow"
   - Monitor build and deployment progress

3. **Verify Deployment:**
   ```bash
   kubectl get deployment -n kube-news
   kubectl get pods -n kube-news
   kubectl get svc -n kube-news
   ```

---

## Sign-Off

- **Setup Completed:** 2026-07-01
- **Status:** Ready for testing
