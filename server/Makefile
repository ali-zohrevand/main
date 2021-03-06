

# ALL VARS get overidden in your own "env.mk"

SELF_DIR := $(dir $(lastword $(MAKEFILE_LIST)))

# Grab necessary submodules, in case the repo was cloned without --recursive
#$(SELF_DIR)/.ci/common.mk:
#	git submodule update --init --recursive

# Incldue your env.mk
# DEVELOPER: make a env.mk by coping env.template.mk and change to whatever you need.
include $(SELF_DIR)/env.template.mk


help:  ## Display this help
	@awk 'BEGIN {FS = ":.*##"; printf "\nUsage:\n  make \033[36m<target>\033[0m\n"} /^[a-zA-Z_-]+:.*?##/ { printf "  \033[36m%-15s\033[0m %s\n", $$1, $$2 } /^##@/ { printf "\n\033[1m%s\033[0m\n", substr($$0, 5) } ' $(MAKEFILE_LIST)


print: ## print
	@echo
	@echo LIB_NAME: $(LIB_NAME)
	@echo LIB: $(LIB)
	@echo LIB_BRANCH: $(LIB_BRANCH)
	@echo LIB_FSPATH: $(LIB_FSPATH)
	@echo

	@echo
	@echo SAMPLE_NAME: $(SAMPLE_NAME)
	@echo SAMPLE_FSPATH: $(SAMPLE_FSPATH)
	@echo

git-clone:
	mkdir -p $(LIB_FSPATH)
	cd $(LIB_FSPATH) && cd .. && rm -rf $(LIB_NAME) && git clone ssh://git@$(LIB).git
	cd $(LIB_FSPATH) && git checkout $(LIB_BRANCH)
git-pull:
	cd $(LIB_FSPATH) && git pull
git-clean:
	rm -rf $(LIB_FSPATH)

code:
	code $(LIB_FSPATH)

dep:
	# setup-proto-go:
	go get -u github.com/golang/protobuf/protoc-gen-go
	go install github.com/golang/protobuf/protoc-gen-go

gen-proto:
	#pub global activate protoc_plugin
	mkdir -p $(SAMPLE_FSPATH)/pkg/api/v1/
ifeq ($(OS),Windows_NT)
	protoc chat.proto --proto_path="$(PROTO_PATH)/server/api/proto/v1/"  --proto_path=. --plugin= --go_out=plugins=grpc:"$(PROTO_OUTPUT)/pkg/api/v1/"
else
	protoc chat.proto --proto_path=$(LIB_FSPATH)/server/api/proto/v1/  --proto_path=. --plugin= --go_out=plugins=grpc:$(SAMPLE_FSPATH)/pkg/api/v1/
endif

### cloud

cloud-auth:
	gcloud auth login
	gcloud config set project $(CLOUD_PROJECT_ID)

	gcloud config set run/region europe-west1
cloud-open:
	# run
	open https://console.cloud.google.com/run?project=$(CLOUD_PROJECT_ID)
	# container
	open https://console.cloud.google.com/cloud-build/builds?project=$(CLOUD_PROJECT_ID)
	# store
	open https://console.cloud.google.com/storage/browser?project=$(CLOUD_PROJECT_ID)
cloud-build:
	echo $(CLOUD_PROJECT_ID)
	cd $(LIB_FSPATH) && gcloud builds submit --tag gcr.io/$(CLOUD_PROJECT_ID)/identicon-generator
cloud-deploy:
	cd $(LIB_FSPATH) && gcloud beta run deploy --image gcr.io/$(CLOUD_PROJECT_ID)/identicon-generator --platform managed



cloud-client-get:
	# Change to use a HTTP Request that hits the API running on the cloud
	# Request needs to be a Http Form post
	curl $(CLOUD_PROJECT_URL)/identicon/generate?name=joe1



### local

export PORT=8080
local-serve:
	# works
	cd $(SAMPLE_FSPATH)/cmd/server && go run .

local-client-get:
	# TODO for testing
	cd $(SAMPLE_FSPATH)/cmd/cli && go run . -name joe
	


