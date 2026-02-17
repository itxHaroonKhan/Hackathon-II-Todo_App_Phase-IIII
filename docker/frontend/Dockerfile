# Multi-stage build with dependency caching
FROM node:18.20.4-alpine AS deps
WORKDIR /app
COPY package.json package-lock.json* ./
RUN npm ci --only=production --legacy-peer-deps && npm cache clean --force

# Build stage
FROM node:18.20.4-alpine AS builder
WORKDIR /app
COPY package.json package-lock.json* ./
RUN npm ci --legacy-peer-deps && npm cache clean --force
COPY . .
ENV NEXT_TELEMETRY_DISABLED 1
RUN npm run build

# Production runtime stage
FROM node:18.20.4-alpine AS runner
WORKDIR /app

# Create non-root user for security
RUN addgroup --system --gid 1001 nodejs && \
    adduser --system --uid 1001 nextjs

# Security and performance environment variables
ENV NODE_ENV=production
ENV NEXT_TELEMETRY_DISABLED=1

# Copy only necessary files from build stage with proper ownership
COPY --from=deps --chown=nextjs:nodejs /app/node_modules ./node_modules
COPY --from=builder --chown=nextjs:nodejs /app/public ./public
COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./output
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static

# Set non-root user
USER nextjs

EXPOSE 3000
ENV PORT=3000

# Set correct working directory for Next.js standalone output
WORKDIR /app/output

# Health check for Kubernetes
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD wget --no-verbose --tries=1 --spider http://localhost:3000 || exit 1

# Start the application
CMD ["node", "server.js"]