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

# Authentication With ISTIO:

Istio allow us to handle logic separately by using built-in object that allow us to pass all the heavy lifting to ISTIO without the need to write our own custom logic, this can happen using `RequestAuthentication` & `AuthorizationPolicy`, this will allow us to setup a provider for authenticating JWT token like `firebase,oAuth,etc..` and also specify specific routing rules

`RequestAuthentication` is responsible of establishing the connection between our `istio-ingress gateway` and the 3rd party provider, and it also define the behavior of the `envoy` proxy of what to do with the information from the jwt decoding

`AuthorizationPolicy` on the other hand allow us to deal with setting up custom routing rules, such as allow some rules to be accessed without the need to `authenticate`
allow us to specify some certain `claims` matching for certain routes, like user should have a role of `admin` to access the urls that start with `/admin/*`

Important to note that ISTIO `RequestAuthentication` only validate the requests coming from the selected pod in our case we are selecting the `ingress gateway` which creates a load balancer in the AWS, this will allow us to authenticate requests from `end users` and not `service to service` communication

# Attached BuildSpec:

the attached buildspec can be used to build any service using ECR, it uses the above tagging policy to build, tag and push the image
