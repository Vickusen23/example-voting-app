# Stage 1: Build & Prune Dependencies
FROM node:22-alpine AS builder
WORKDIR /usr/local/app

# Copy dependency manifests
COPY package*.json ./

# Install only production dependencies
RUN npm ci --only=production

# Stage 2: Final Secure Lightweight Image
FROM node:22-alpine
WORKDIR /usr/local/app

# Upgrade system packages to patch OS vulnerabilities
RUN apk update && apk upgrade --no-cache

# Run as non-root user for security
USER node

# Copy dependencies and source code from builder
COPY --from=builder /usr/local/app/node_modules ./node_modules
COPY . .

EXPOSE 80
ENV PORT=80

CMD ["node", "server.js"]
