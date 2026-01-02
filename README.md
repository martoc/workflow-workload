[![Tag and Release](https://github.com/martoc/workflow-workload/actions/workflows/tag.yml/badge.svg)](https://github.com/martoc/workflow-workload/actions/workflows/tag.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

# workflow-workload

Reusable GitHub Actions workflows for application workloads. Provides standardised build, test, container creation, and deployment processes for Go and Node.js applications using AWS.

## Features

- Go application workflow with testing and code coverage
- Node.js application workflow
- Automatic semantic versioning with `action-tag`
- Container builds with `action-container-build`
- GitHub release creation with `action-release`
- AWS deployment with CloudFormation using `action-deploy`
- Codecov integration for Go projects

## Quick Start

### Go Workflow

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

### Node.js Workflow

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

## Documentation

- [Usage Guide](./docs/USAGE.md) - Detailed usage instructions and examples
- [Code Style](./docs/CODESTYLE.md) - Code style guidelines for contributors

## Available Workflows

| Workflow | Description |
|----------|-------------|
| `go.yml` | Build, test, and deploy Go applications |
| `node.yml` | Build and deploy Node.js applications |

## Inputs

| Input | Required | Description |
|-------|----------|-------------|
| `region` | Yes | AWS region for deployment |
| `environment` | Yes | Target environment |
| `container-registry-namespace` | No | Container registry namespace |
| `workload-name` | Yes | Name of the workload |
| `workload-type` | Yes | Type of workload |
| `workload-type-ref` | No | Git ref for workload type (default: `main`) |

## Licence

This project is licenced under the MIT Licence - see the [LICENCE](LICENSE) file for details.
