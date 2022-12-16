AZURE_CLI_VERSION := 2.42.0-1~focal
AZURE_CLI_VERSION_NUMBER := $(word 1,$(subst -, ,$(AZURE_CLI_VERSION)))
IMAGE_NAME := ${IMAGE_REGISTRY_URL}/azure-cli-runner

build:
	docker build . --build-arg AZURE_CLI_VERSION=$(AZURE_CLI_VERSION) -t "$(IMAGE_NAME):latest" -t "$(IMAGE_NAME):${GIT_COMMIT_SHA}" -t "$(IMAGE_NAME):azure-cli-v$(AZURE_CLI_VERSION_NUMBER)"
push:
	docker push "$(IMAGE_NAME)" --all-tags