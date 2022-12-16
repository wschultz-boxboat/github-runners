# Introduction
[GitHub Documentation on Recommended Self-Hosted Runner Autoscaling Solutions](https://docs.github.com/en/enterprise-server@3.4/actions/hosting-your-own-runners/autoscaling-with-self-hosted-runners#recommended-autoscaling-solutions)

The [Actions Runner Controller](https://github.com/actions/actions-runner-controller) owns docker images for use as self-hosted GitHub runners on kubernetes. These images come pre-setup to attach to GitHub and run basic GitHub Action Workflows. However, there is sometimes a need to include additional software on the runner and this is where a custom docker image thrives. These steps will help to get from the standard self-hosted GitHub runner to a custom docker image extending the standard self-hosted GitHub runner with additional software and configurations.

## Disclaimer
Ideally the standard docker image owned by the Actions Runner Controller is used and required software is installed or setup using GitHub Marketplace Actions such as hashicorp/setup-terraform to minimize maintenance of the runners such as updating software or configuring the runners to support multiple versions of the same software.

# Prerequisites
* An existing Kubernetes cluster
* Actions Runner Controller deployed to the existing Kubernetes Cluster
* A container registry to push docker images to
* A method to build images locally, on a remote system, or through CI/CD

# Guided Steps
1. Identify base [image](https://hub.docker.com/u/summerwind) to use   
    a. A decent starting image would be the [summerwind/actions-runner](https://hub.docker.com/r/summerwind/actions-runner)
2. Identify latest stable image tag for that image   
    a. As of 2022-11-30 this tag would be "v2.299.1-ubuntu-20.04"
3. Create a new Dockerfile with a [FROM](https://docs.docker.com/engine/reference/builder/#from) using the items from steps 1 and 2 **[i]**
4. Further build out the Dockerfile to install software and setup custom configurations  
    a. [Example](https://github.com/wschultz-boxboat/github-runners/blob/main/azure-cli/Dockerfile) Dockerfile installing azure cli, kubectl, and helm
5. Build the image using docker, podman, or another container imaging tool **[ii]** **[v]**
6. Push the image to a team or organization owned container registry **[iii]**
7. Create a Runner Deployment resource file **[iv]**   
    a. [Example](https://github.com/wschultz-boxboat/github-runners/blob/main/azure-cli/runner-deployment.yaml)  
    b. [Documentation](https://github.com/actions/actions-runner-controller/blob/master/docs/detailed-docs.md#usage)
8. Deploy the Runner Deployment resource to Kubernetes
9. Test the new runner using a [GitHub Action workflow](https://docs.github.com/en/actions/hosting-your-own-runners/using-self-hosted-runners-in-a-workflow)

### Notes:

**[i]** Rather than referencing the actions-runner image directly, a team or organization could choose to pull this image version and push to a container registry owned by them. The benefit here for that team or organization would include incorporating team or organization image security scanning standards and providing a curated list of approved images to the individuals building and maintaining custom runner images.  
**[ii]** It is recommended to tag an image with more than just "latest" to allow processes to pin to a specific version of the image. This could be a version tag such as "v1.0.2", a git commit sha, or another unique identifier for that specific image build.  
**[iii]** Any kubernetes cluster that will be utilizing this image will require rights to pull this image such as using image pull secrets or in the case of Azure Kubernetes Service (AKS) and Azure Container Registry (ACR) the provisioning of AcrPull RBAC.  
**[iv]** Utilizing [labels](https://github.com/actions/actions-runner-controller/blob/master/docs/detailed-docs.md#runner-labels) can ensure only workflows that require the specific software or configuration pick up the custom runner for their workflow action runs.  
**[v]** If the image is being built within a private network, ensure firewall rules are in place for any software that will be installed during the image build process

# Auto Scaling
The Actions Runner Controller has great [documentation](https://github.com/actions/actions-runner-controller/blob/master/docs/detailed-docs.md#autoscaling) on setting up auto scaling for a RunnerDeployment. That said, of the three currently listed, [PercentageRunnersBusy](https://github.com/actions/actions-runner-controller/blob/master/docs/detailed-docs.md#pull-driven-scaling) and [Webhook Driven Scaling](https://github.com/actions/actions-runner-controller/blob/master/docs/detailed-docs.md#webhook-driven-scaling) are the only low maintenance options. The metric TotalNumberOfQueuedAndInProgressWorkflowRuns requires specifying repositories for monitoring and does not currently support scoping to an organization so as the number of repositories increase under the support of the self-hosted runner that's created, the list of repositories to include within this metric increases as well.

Polling metrics must be configured such that they stay within the rate limit on the GitHub API. This [configuration](https://github.com/actions/actions-runner-controller/blob/master/docs/detailed-docs.md#pull-driven-scaling) is at the controller level and not the auto scaling resource itself. 

# Suggestions
* Create a CI pipeline to build the image for quick feedback loops
* Utilize a Makefile to create a uniform build and push process