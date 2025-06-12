# Image Update and Redeploy

A CLI tool to update and deploy a service instance on Railway. This tool updates a service's Docker image and triggers a redeployment in a single command.

## Features

- Updates Railway service instances with new Docker images
- Automatically redeploys the service after updating
- Simple CLI interface with short and long options

## Prerequisites

- Node.js (v18 or higher)
- API Token (Must be an Account or Team API Token, a Project Token will not work)
- Service ID and Environment ID from your Railway project

## Installation

1. Clone this repository:
```bash
git clone https://github.com/brody192/image-update-and-redeploy.git
cd image-update-and-redeploy
```

2. Install dependencies:
```bash
npm install
```

3. Build the project:
```bash
npm run build
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
npm start -- --serviceId=12345678-1234-1234-1234-123456789012 --environmentId=87654321-4321-4321-4321-210987654321 --image=myapp:latest
```

### Using Short Options
```bash
npm start -- -s 12345678-1234-1234-1234-123456789012 -e 87654321-4321-4321-4321-210987654321 -i myapp:v1.2.3
```

### Development Mode
```bash
npm run dev -- --serviceId=12345678-1234-1234-1234-123456789012 --environmentId=87654321-4321-4321-4321-210987654321 --image=myapp:development
```

### Using Built Distribution
```bash
node dist/main.js --serviceId=12345678-1234-1234-1234-123456789012 --environmentId=87654321-4321-4321-4321-210987654321 --image=myapp:production
```

### With Different Image Registries
```bash
# Docker Hub
npm start -- -s 12345678-1234-1234-1234-123456789012 -e 87654321-4321-4321-4321-210987654321 -i username/myapp:latest

# GitHub Container Registry
npm start -- -s 12345678-1234-1234-1234-123456789012 -e 87654321-4321-4321-4321-210987654321 -i ghcr.io/username/myapp:main

# Amazon ECR
npm start -- -s 12345678-1234-1234-1234-123456789012 -e 87654321-4321-4321-4321-210987654321 -i 123456789012.dkr.ecr.us-west-2.amazonaws.com/myapp:latest
```

## How It Works

1. **Validation**: The tool validates that all required arguments are provided and properly formatted
2. **Authentication**: Uses your Railway API Token to authenticate with the Railway GraphQL API
3. **Update**: Updates the service instance with the new Docker image
4. **Redeploy**: Triggers a redeployment of the service with the updated image

## Finding Your IDs

To find your Railway service and environment IDs:

1. Go to your Railway project dashboard
2. Navigate to your service
3. Check the URL - it contains both IDs:
   ```
   https://railway.com/project/{projectId}/service/{serviceId}?environmentId={environmentId}
   ```

## Scripts

- `npm run build` - Build the project using esbuild
- `npm start` - Run the built version from dist/
- `npm run dev` - Run directly from source (development)

## License

MIT License - see [LICENSE](LICENSE) file for details.