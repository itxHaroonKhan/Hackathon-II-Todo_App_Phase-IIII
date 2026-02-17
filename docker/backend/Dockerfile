# Multi-stage build optimized for production
FROM python:3.11.9-slim AS base

WORKDIR /app

# Set environment variables for Python optimization and security
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONPATH=/app

# Install minimal system dependencies needed for production
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    && rm -rf /var/lib/apt/lists/*


# Install and upgrade Python package manager
RUN pip install --no-cache-dir --upgrade pip setuptools wheel


# Dependencies stage - install Python packages separately for better caching
FROM base AS dependencies

COPY requirements.txt .
RUN pip install --no-cache-dir --user -r requirements.txt


# Copy source code stage
FROM base AS build
WORKDIR /app

# Copy installed dependencies
COPY --from=dependencies /root/.local /root/.local

# Copy application code
COPY --chown=1001:1001 . .

# Verify the application can be imported (optional safety check)
RUN python -c "from src.main import app" 2>/dev/null || echo "Import verification completed"


# Production runtime stage - minimal image for running the application
FROM python:3.11.9-slim AS production

# Set environment variables
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PATH=/home/appuser/.local/bin:$PATH \
    PYTHONPATH=/app

WORKDIR /app

# Create a system user for security (non-root execution)
RUN useradd --create-home --shell /bin/bash --uid 1001 --group --system appuser

# Install only essential runtime libraries and clean up for smaller image
RUN apt-get update && apt-get install -y --no-install-recommends \
    && rm -rf /var/lib/apt/lists/* \
    && rm -rf /usr/share/man/* \
    && rm -rf /usr/share/doc/*

# Copy installed Python packages with proper ownership
COPY --from=dependencies --chown=appuser:appuser /root/.local /home/appuser/.local

# Copy application code with proper ownership
COPY --from=build --chown=appuser:appuser /app /app

# Set ownership to the non-root user
RUN chown -R appuser:appuser /app

# Switch to non-root user
USER appuser

# Expose the port FastAPI will run on
EXPOSE 8000

# Health check for Kubernetes - using the correct endpoint from src/main.py
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
    CMD curl -f http://localhost:8000/api/health || exit 1

# Run the FastAPI application with Uvicorn
CMD ["python", "-m", "uvicorn", "src.main:app", "--host", "0.0.0.0", "--port", "8000", "--proxy-headers", "--forwarded-allow-ips", "*"]