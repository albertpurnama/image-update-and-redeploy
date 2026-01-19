# Image Update and Redeploy

A CLI tool to update and deploy a service instance on Railway. This tool updates a service's Docker image and triggers a redeployment in a single command.

## Features

- Updates Railway service instances with new Docker images
- Automatically redeploys the service after updating
- Simple CLI interface with short and long options

## Prerequisites

- Bun (for local usage) or Node.js (v18 or higher)
- Railway API Token (Must be an Account or Team API Token, a Project Token will not work)
- Service ID and Environment ID from your Railway project

## Installation

1. Clone this repository:
```bash
git clone https://github.com/albertpurnama/image-update-and-redeploy.git
cd image-update-and-redeploy
```

2. Install dependencies:
```bash
bun install
```

3. Build the project:
```bash
bun run build
```

## Environment Variables

Set your Railway API Token as an environment variable:

```bash
export RAILWAY_API_TOKEN=your-railway-api-token-here
```

## CLI Options

```
┌─────────────────┬─────────┬────────────┬──────────────────────────────────────┐
│ Option          │ Alias   │ Type       │ Description                          │
├─────────────────┼─────────┼────────────┼──────────────────────────────────────┤
│ --serviceId     │ -s      │ UUID       │ Railway service ID (required)        │
│ --environmentId │ -e      │ UUID       │ Railway environment ID (required)    │
│ --image         │ -i      │ String     │ Docker image name/tag (required)     │
└─────────────────┴─────────┴────────────┴──────────────────────────────────────┘
```

## Usage Examples

### Basic Usage
```bash
bun start -- --serviceId=12345678-1234-1234-1234-123456789012 --environmentId=87654321-4321-4321-4321-210987654321 --image=myapp:latest
```

### Using Short Options
```bash
bun start -- -s 12345678-1234-1234-1234-123456789012 -e 87654321-4321-4321-4321-210987654321 -i myapp:v1.2.3
```

### Development Mode
```bash
bun run dev -- --serviceId=12345678-1234-1234-1234-123456789012 --environmentId=87654321-4321-4321-4321-210987654321 --image=myapp:development
```

### Using Built Distribution
```bash
node dist/main.js --serviceId=12345678-1234-1234-1234-123456789012 --environmentId=87654321-4321-4321-4321-210987654321 --image=myapp:production
```

### With Different Image Registries
```bash
# Docker Hub
bun start -- -s 12345678-1234-1234-1234-123456789012 -e 87654321-4321-4321-4321-210987654321 -i username/myapp:latest

# GitHub Container Registry
bun start -- -s 12345678-1234-1234-1234-123456789012 -e 87654321-4321-4321-4321-210987654321 -i ghcr.io/username/myapp:main

# Amazon ECR
bun start -- -s 12345678-1234-1234-1234-123456789012 -e 87654321-4321-4321-4321-210987654321 -i 123456789012.dkr.ecr.us-west-2.amazonaws.com/myapp:latest
```

## How It Works

1. **Validation**: The tool validates that all required arguments are provided and properly formatted
2. **Authentication**: Uses your Railway API Token to authenticate with the Railway GraphQL API
3. **Update**: Updates the service instance with the new Docker image
4. **Redeploy**: Triggers a redeployment of the service with the updated image

## Using as a GitHub Action

This project can be used as a reusable GitHub Action in any of your repositories. This is useful for automatically updating and deploying your Railway services as part of your CI/CD pipeline.

### Setup

1. In your repository that uses this action, add the required secrets:
   - Go to **Settings → Secrets and variables → Actions**
   - Add the following secrets:
     - `RAILWAY_SERVICE_ID` - Your Railway service ID
     - `RAILWAY_ENV_ID` - Your Railway environment ID
     - `RAILWAY_API_TOKEN` - Your Railway API token

2. Create or update your workflow file (e.g., `.github/workflows/deploy.yml`)

### Example Workflows

#### Deploy on push to main branch
```yaml
name: Deploy to Railway

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Update Railway Service
        uses: albertpurnama/image-update-and-redeploy@main
        with:
          serviceId: ${{ secrets.RAILWAY_SERVICE_ID }}
          environmentId: ${{ secrets.RAILWAY_ENV_ID }}
          image: ghcr.io/yourorg/yourapp:${{ github.sha }}
          railwayApiToken: ${{ secrets.RAILWAY_API_TOKEN }}
```

#### Deploy manually with workflow dispatch
```yaml
name: Manual Railway Deploy

on:
  workflow_dispatch:
    inputs:
      image:
        description: 'Docker image tag to deploy'
        required: true
        default: 'latest'

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Update Railway Service
        uses: albertpurnama/image-update-and-redeploy@main
        with:
          serviceId: ${{ secrets.RAILWAY_SERVICE_ID }}
          environmentId: ${{ secrets.RAILWAY_ENV_ID }}
          image: ghcr.io/yourorg/yourapp:${{ github.event.inputs.image }}
          railwayApiToken: ${{ secrets.RAILWAY_API_TOKEN }}
```

#### Deploy as part of a multi-step workflow
```yaml
name: Build and Deploy

on:
  push:
    branches:
      - main

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Build and push Docker image
        run: |
          docker build -t ghcr.io/yourorg/yourapp:${{ github.sha }} .
          docker push ghcr.io/yourorg/yourapp:${{ github.sha }}

      - name: Update Railway Service
        uses: albertpurnama/image-update-and-redeploy@main
        with:
          serviceId: ${{ secrets.RAILWAY_SERVICE_ID }}
          environmentId: ${{ secrets.RAILWAY_ENV_ID }}
          image: ghcr.io/yourorg/yourapp:${{ github.sha }}
          railwayApiToken: ${{ secrets.RAILWAY_API_TOKEN }}
```

### Action Inputs

| Input | Description | Required |
|-------|-------------|----------|
| `serviceId` | Railway service ID (UUID) | Yes |
| `environmentId` | Railway environment ID (UUID) | Yes |
| `image` | Docker image name/tag to deploy | Yes |
| `railwayApiToken` | Railway API Token (Account or Team token) | Yes |

## Finding Your IDs

To find your Railway service and environment IDs:

1. Go to your Railway project dashboard
2. Navigate to your service
3. Check the URL - it contains both IDs:
   ```
   https://railway.com/project/{projectId}/service/{serviceId}?environmentId={environmentId}
   ```

## Scripts

- `bun run build` - Build the project using esbuild
- `bun start` - Run the built version from dist/
- `bun run dev` - Run directly from source (development)

## License

MIT License - see [LICENSE](LICENSE) file for details.