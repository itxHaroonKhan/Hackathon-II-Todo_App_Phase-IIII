# Docker Build Instructions

This document contains instructions for building and running the Docker images for both the Next.js frontend and FastAPI backend applications.

## Backend (FastAPI) Docker Image

### Build Command:
```bash
cd /path/to/Both/backend
docker build -t fastapi-backend .
```

### Run Command (if using port 7860):
```bash
docker run -p 7860:7860 fastapi-backend
```

### Environment Variables:
You can pass environment variables using the -e flag:
```bash
docker run -p 7860:7860 \
  -e DATABASE_URL=postgresql://user:pass@host:port/db \
  -e COHERE_API_KEY=your-api-key \
  fastapi-backend
```

## Frontend (Next.js) Docker Image

### Build Command:
```bash
cd /path/to/Both/frontend
docker build -t nextjs-frontend .
```

### Run Command:
```bash
docker run -p 3000:3000 nextjs-frontend
```

## Multi-stage Build Benefits

### Backend:
- Dependencies are installed in a separate build stage for faster rebuilds
- Production image only contains necessary files and dependencies
- Application runs as a non-root user for security
- Includes health checks for Kubernetes readiness
- Optimized base image size using python:3.11-slim

### Frontend:
- Dependencies are installed in a separate build stage
- Uses Next.js standalone output mode for optimal performance
- Production image is minimal and only contains runtime files
- Application runs as non-root user for security
- Alpine base image keeps the size minimal

## Security Features

- Both applications run as non-root users (UID 1001)
- Minimal base images (alpine/slim variants)
- No unnecessary packages included in final images
- Removed build-time dependencies from final images

## Kubernetes Optimization

- Proper health checks are implemented
- Stable ports for both services (frontend: 3000, backend: 7860)
- Optimized image size for faster pull times in K8s clusters
- Environment variable support for configuration
- Non-root user execution for security compliance