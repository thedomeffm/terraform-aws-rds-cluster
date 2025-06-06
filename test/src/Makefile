export TERRAFORM_VERSION ?= $(shell curl -s https://checkpoint-api.hashicorp.com/v1/check/terraform | jq -r -M '.current_version' | cut -d. -f1)
export GOTOOLCHAIN = local

.DEFAULT_GOAL : all
.PHONY: all

## Default target
all: test

.PHONY : init
## Initialize tests
init:
	@exit 0

.PHONY : test
## Run tests
test: init
	go mod download
	# Allow more parallel tests than there are CPU cores because most of the time tests are just waiting for AWS
	go test -v -timeout 60m -parallel 8

## Run tests in docker container
docker/test:
	docker run --name terratest --rm -it -e AWS_ACCESS_KEY_ID -e AWS_SECRET_ACCESS_KEY -e AWS_SESSION_TOKEN -e GITHUB_TOKEN -e GOTOOLCHAIN \
		-e PATH="/usr/local/terraform/$(TERRAFORM_VERSION)/bin:/go/bin:/usr/local/go/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin" \
		-v $(CURDIR)/../../:/module/ cloudposse/test-harness:latest -C /module/test/src test

.PHONY : clean
## Clean up files
clean:
	rm -rf ../../examples/complete/*.tfstate*
	rm -rf ../../examples/serverlessv2_postgres/*.tfstate*
