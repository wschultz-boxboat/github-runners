# Use
The actions runner controller is a project adopted by GitHub for managing self-hosted runners at scale on Kubernetes. The actions runner controller will include the Custom Resource Definitions (CRDs) for creating self-hosted runner deployments along with other resources that a team/organization may find useful.

# Pre-Requesites
* Access to install a GitHub App
* [Kubectl](https://kubernetes.io/docs/tasks/tools/) installed locally
* [Helm](https://helm.sh/docs/helm/helm_install/) installed locally
* Permissions to create namespaces and resources within them on Kubernetes
* Authentication configured for Kubectl

# Installation
The action runner controller documents are a great resource, so we'll be linking to them as references rather than re-creating the steps.

1. Install an ingress controller on Kubernetes
2. Install a certificate manager on Kubernetes
3. Create and install a [GitHub App for authentication](https://github.com/actions/actions-runner-controller/blob/master/docs/detailed-docs.md#deploying-using-github-app-authentication)
4. Add the actions runner controller repo to helm
```
helm repo add actions-runner-controller https://actions-runner-controller.github.io/actions-runner-controller
```
5. Determine version to deploy [i]
```
helm search repo actions-runner-controller --versions
```
6. Install Actions Runner Controller
```
helm install -n github-runners \
  --create-namespace \
  --set=authSecret.create=true \
  --set=authSecret.github_app_id="<app id>" \
  --set=authSecret.github_app_installation_id="<installation id>" \
  --set=authSecret.github_app_private_key="$(cat /path/to/github-app.key)" \
  --version "<chart version from step 5>" \
  actions-runner-controller actions-runner-controller/actions-runner-controller
```
7. Deploy runner resources to Kubernetes

### Notes
**[i]** For a fresh installation, the latest version is probably best

# Upgrade
1. Identify version to upgrade to
```
helm search repo actions-runner-controller --versions
```
2. Upgrade Actions Runner Controller
```
helm upgrade -n github-runners \
  --reuse-values \
  --version "<chart version from step 1>" \
  actions-runner-controller actions-runner-controller/actions-runner-controller
```