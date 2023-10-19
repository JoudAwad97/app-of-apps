# Semantic Versioning that will be used

```yaml
# Development ArgoCD Application
metadata:
  annotations:
      argocd-image-updater.argoproj.io/image-list: my-container-image=quay.io/my-org/my-image
      argocd-image-updater.argoproj.io/write-back-method: git
      argocd-image-updater.argoproj.io/my-container-image.update-strategy: latest

# Staging ArgoCD Application
metadata:
  annotations:
    argocd-image-updater.argoproj.io/image-list: my-container-image=quay.io/my-org/my-image
    argocd-image-updater.argoproj.io/write-back-method: git
    argocd-image-updater.argoproj.io/my-container-image.update-strategy: semver
    argocd-image-updater.argoproj.io/my-container-image.tag-suffix: -uat

# Production ArgoCD Application
metadata:
  annotations:
    argocd-image-updater.argoproj.io/image-list: my-container-image=quay.io/my-org/my-image
    argocd-image-updater.argoproj.io/write-back-method: git
    argocd-image-updater.argoproj.io/my-container-image.update-strategy: semver
```

### Thinking about how we are going to version the app:

1. once we merge a normal PR to the main branch we trigger a build step that will use the commitHash and tag the image with it
2. when we promote from `DEV -> UAT`, we use libraries to increment the semver to us, this will happen depending on the PR's tag we have `(fix,feat, Breaking changes)`, this will generate a version incremental and update the github tags along with the `package.json` file, build a docker image and tag it with the updated `semver` that we have
3. when promoting `UAT -> PROD`, we promote the same version we have in UAT, which allow us to change the the tag from `x.y.z-uat` into `x.y.z` then we tag the image and push it to DockerHub

all these updates will reflect on the above versioning that we mention allowing us to keep track of the right version between each env

# Attached BuildSpec:

the attached buildspec can be used to build any service using ECR, it uses the above tagging policy to build, tag and push the image
