FROM summerwind/actions-runner:v2.299.1-ubuntu-20.04

# Install Required Packages for Tool Installations
RUN sudo apt-get update -y && \
	sudo apt-get install -y ca-certificates curl apt-transport-https lsb-release gnupg  && \
	sudo apt-get clean && \
	sudo rm -rf /var/lib/apt/lists/*

# Setup Azure CLI Source
RUN curl -sL https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/microsoft.gpg > /dev/null && \
  	AZ_REPO=$(lsb_release -cs) && \
	echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ $AZ_REPO main" | sudo tee /etc/apt/sources.list.d/azure-cli.list 

ARG AZURE_CLI_VERSION

# Install Tools
RUN sudo apt-get update && \
    sudo apt-get install -y azure-cli=${AZURE_CLI_VERSION} && \
	sudo apt-get clean && \
	sudo rm -rf /var/lib/apt/lists/*
