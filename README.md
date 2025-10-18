# K3s Deploy - Shared GitHub Workflows

Reusable GitHub Actions workflows for building and deploying applications to homelab K3s clusters using BuildKit and kubectl.

## Usage

### Basic Example

```yaml
name: Build and Deploy

on:
  push:
    branches:
      - main

jobs:
  deploy:
    uses: h4ks-com/k3s-deploy/.github/workflows/k3s-deploy.yaml@main
    with:
      namespace: my-app
      deployment-name: my-app
      services: |
        [
          {
            "context": ".",
            "image": "my-app",
            "containers": ["app"]
          }
        ]
```

### Multiple Services Example

```yaml
name: Build and Deploy Multi-Service App

on:
  push:
    branches:
      - main

jobs:
  deploy:
    uses: h4ks-com/k3s-deploy/.github/workflows/k3s-deploy.yaml@main
    with:
      namespace: worldguess
      deployment-name: worldguess-backend
      services: |
        [
          {
            "context": "./frontend",
            "image": "worldguess-frontend",
            "containers": ["frontend-builder"]
          },
          {
            "context": ".",
            "dockerfile": "./pipelines/Dockerfile",
            "image": "worldguess-pipelines",
            "containers": []
          },
          {
            "context": "./backend",
            "image": "worldguess-backend",
            "containers": ["backend"]
          }
        ]
      job-config: |
        {
          "name": "worldguess-pipelines",
          "template": "worldguess-pipelines"
        }
```

## Input Parameters

### Required Inputs

| Input | Description | Example |
|-------|-------------|---------|
| `services` | JSON array of service configurations | See examples above |
| `namespace` | Kubernetes namespace for deployment | `my-app` |

### Optional Inputs

| Input | Description | Default |
|-------|-------------|---------|
| `deployment-name` | Main deployment name to update | - |
| `job-config` | JSON config for job restart | - |
| `registry` | Docker registry URL | `192.168.2.201:5000` |
| `buildkit-endpoint` | BuildKit endpoint | `tcp://buildkit.buildkit.svc.cluster.local:1234` |

## Service Configuration Schema

Each service in the `services` array supports:

```json
{
  "context": "./path/to/build/context",
  "dockerfile": "./path/to/Dockerfile",  // Optional, defaults to Dockerfile in context
  "image": "image-name",
  "containers": ["container1", "container2"]  // Container names to update in deployment
}
```

## Job Configuration Schema

```json
{
  "name": "job-name",
  "template": "job-template-name"  // Optional, defaults to name
}
```
