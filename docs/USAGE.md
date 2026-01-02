# Usage

## Overview

This repository provides reusable GitHub Actions workflows for application workloads. It simplifies the CI/CD workflow by automating build, test, container creation, and deployment processes for Go and Node.js applications.

## Available Workflows

- [Go Workflow](#go-workflow)
- [Node.js Workflow](#nodejs-workflow)

## Go Workflow

The `go.yml` workflow is a callable workflow for Go-based applications.

### Workflow Details

#### Build Job

- **Runner**: `ubuntu-24.04`
- **Go Version**: `1.21.0`
- **Steps**:
  - Checkout code
  - Setup Go environment
  - Tag release using `martoc/action-tag`
  - Configure AWS credentials
  - Run `make init` and `make build`
  - Run integration tests with `make run-integration-tests`
  - Upload coverage to Codecov
  - Build container using `martoc/action-container-build`
  - Create GitHub release using `martoc/action-release`

#### Deploy Job

- Runs after successful build (not on pull requests)
- Deploys using CloudFormation templates
- Uses `martoc/action-deploy` for deployment

### Inputs

| Input | Required | Description |
|-------|----------|-------------|
| `region` | Yes | AWS region for deployment |
| `environment` | Yes | Target environment |
| `container-registry-namespace` | No | Container registry namespace |
| `workload-name` | Yes | Name of the workload |
| `workload-type` | Yes | Type of workload |
| `workload-type-ref` | No | Git ref for workload type (default: `main`) |

### Example

```yaml
name: Go Workload

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build-and-deploy:
    permissions:
      contents: write
      id-token: write
    uses: martoc/workflow-workload/.github/workflows/go.yml@v0
    with:
      region: eu-west-1
      environment: production
      workload-name: my-service
      workload-type: lambda
    secrets: inherit
```

### Requirements

Your repository must include:

1. A `Makefile` with `init`, `build`, and `run-integration-tests` targets
2. A `Dockerfile` for container builds

## Node.js Workflow

The `node.yml` workflow is a callable workflow for Node.js applications.

### Workflow Details

#### Build Job

- **Runner**: `ubuntu-24.04`
- **Node Version**: `22`
- **Steps**:
  - Checkout code
  - Setup Node.js environment
  - Tag release using `martoc/action-tag`
  - Configure AWS credentials
  - Run `npm run init` and `npm run build`
  - Build container using `martoc/action-container-build`
  - Create GitHub release using `martoc/action-release`

#### Deploy Job

- Runs after successful build
- Deploys using CloudFormation templates
- Uses `martoc/action-deploy` for deployment

### Inputs

| Input | Required | Description |
|-------|----------|-------------|
| `region` | Yes | AWS region for deployment |
| `environment` | Yes | Target environment |
| `container-registry-namespace` | No | Container registry namespace |
| `workload-name` | Yes | Name of the workload |
| `workload-type` | Yes | Type of workload |
| `workload-type-ref` | No | Git ref for workload type (default: `main`) |

### Example

```yaml
name: Node Workload

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build-and-deploy:
    permissions:
      contents: write
      id-token: write
    uses: martoc/workflow-workload/.github/workflows/node.yml@v0
    with:
      region: eu-west-1
      environment: production
      workload-name: my-app
      workload-type: ecs
    secrets: inherit
```

### Requirements

Your repository must include:

1. A `package.json` with `init` and `build` scripts
2. A `Dockerfile` for container builds

## Required Secrets

Both workflows require the following secrets:

| Secret | Description |
|--------|-------------|
| `AWS_ROLE_TO_ASSUME` | IAM role ARN for AWS authentication |
| `DOCKER_USERNAME` | Docker Hub username |
| `DOCKER_PASSWORD` | Docker Hub password |
| `PERSONAL_GITHUB_TOKEN` | GitHub token for accessing CloudFormation templates |
| `CODECOV_TOKEN` | Codecov token (Go workflow only) |
