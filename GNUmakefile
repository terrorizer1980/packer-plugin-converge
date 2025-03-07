NAME=converge
BINARY=packer-plugin-${NAME}

COUNT?=1
TEST?=$(shell go list ./...)

.PHONY: dev

build:
	@go build -o ${BINARY}

dev: build
	@mkdir -p ~/.packer.d/plugins/
	@mv ${BINARY} ~/.packer.d/plugins/${BINARY}

run-example: dev
	@packer init ./example
	@packer build ./example

test:
	@go test -count $(COUNT) $(TEST) -timeout=3m

install-gen-deps: ## Install dependencies for code generation
	# packer-sdc allows to code generate everything needed to generate docs and
	# HCL2 specific code.
	@go install github.com/hashicorp/packer-plugin-sdk/cmd/packer-sdc@latest

generate: install-gen-deps
	@go generate ./...

ci-release-docs: install-gen-deps
	@packer-sdc renderdocs -src docs -partials docs-partials/ -dst docs/
	@/bin/sh -c "[ -d docs ] && zip -r docs.zip docs/"

testacc: dev
	@PACKER_ACC=1 go test -count $(COUNT) -v $(TEST) -timeout=120m

