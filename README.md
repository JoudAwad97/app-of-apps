# Semantic Versioning that will be used

```yaml
# Development ArgoCD Application
metadata:
  annotations:
    argocd-image-updater.argoproj.io/image-list: my-repo/my-image:latest

# Staging ArgoCD Application
metadata:
  annotations:
    argocd-image-updater.argoproj.io/image-list: my-repo/my-image:^1.0.0

# Production ArgoCD Application
metadata:
  annotations:
    argocd-image-updater.argoproj.io/image-list: my-repo/my-image:~1.0.0
```

# Attached BuildSpec:

the attached buildspec can be used to build any service using ECR, it uses the above tagging policy to build, tag and push the image
