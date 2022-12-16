# Use
A self-hosted GitHub runner for the [Actions Runner Controller](https://github.com/actions/actions-runner-controller) with [Azure CLI](https://learn.microsoft.com/en-us/cli/azure/) installed to allow authenticating with Microsoft Azure using the [Azure/login](https://github.com/Azure/login) action.

# Maintenance
* Updating the tag of the [summerwind/actions-runner](https://hub.docker.com/r/summerwind/actions-runner/tags) image within the Dockerfile
* Updating the version of Azure CLI to install within the Makefile
* Adding additional tags to the image as needed or requested
* Updating the tag of the image for the RunnerDeployment resource after it has been pushed to the image registry

## Determining Available Azure CLI Versions
1. Grab the actions runner image being used in the Dockerfile
2. Run a container based off the image from step 1 locally
3. Run the install steps to get the Azure CLI package repository configured
4. Update the list of available packages
5. List the versions of azure-cli that can be installed

### Example CLI commands [i]
```
podman machine start
podman run -d -i summerwind/actions-runner:v2.299.1-ubuntu-20.04 /bin/bash
podman exec -it sweet_rhodes /bin/bash

# now within the running container

sudo apt-get update -y && \
    sudo apt-get install -y ca-certificates curl apt-transport-https lsb-release gnupg  && \
	sudo apt-get clean && \
	sudo rm -rf /var/lib/apt/lists/*

curl -sL https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/microsoft.gpg > /dev/null && \
  	AZ_REPO=$(lsb_release -cs) && \
	echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ $AZ_REPO main" | sudo tee /etc/apt/sources.list.d/azure-cli.list 

sudo apt-get update -y
sudo apt list -a azure-cli
```

### Notes
**[i]** Can use docker in place of podman, podman is just what I used on my machine

# Initial Deployment
## GitHub Runners That Can Push to the Image Registry Exist
1. Build the image through CI/CD
2. Push the image to the image registry through CI/CD
3. Deploy the RunnerDeployment resource to the appropriate Kubernetes cluster

## GitHub Runners That Can Push to the Image Registry Do Not Exist
1. Build the image from a machine that can access the image registry
2. Push the image to the image registry from a machie that can access the image registry
3. Deploy the RunnerDeployment resource to the appropriate Kubernetes cluster
4. Assuming this new RunnerDeployment can access the image registry from the Kubernetes cluster it's deployed to, setup a CI/CD pipeline for building and redeploying this image for future changes using this new runner