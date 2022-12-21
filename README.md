# GitHub Runners on Kubernetes

Security concerns with cloud hosted actions runners? With self-hosted runners you can run GitHub workflows/actions within your own private corporate network without having to open firewall ports. 

With Kubernetes, you can use the [GitHub Actions controller](https://github.com/actions/actions-runner-controller) to run GitHub actions [runners](https://actions-runner-controller.github.io/actions-runner-controller/) on your own cluster, where they get provisioned on demand based on the queued GitHub Actions jobs. Once installed, self-hosted runners with Kubernetes can be used just as easily as GitHub hosted runners but are much more customizeable, reusable, and secure inside your private network on Azure.

# Resources for self-hosted GitHub runners

### [Installing Actions Runner Controller](https://github.com/wschultz-boxboat/github-runners/blob/main/docs/actions-runner-controller-installation.md)

### [Creating Custom Runners](https://github.com/wschultz-boxboat/github-runners/blob/main/docs/creating-a-custom-runner.md)