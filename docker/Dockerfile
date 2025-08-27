# Multi-stage build for ReplayWeb.page
# Build stage
FROM node:18-alpine AS builder

WORKDIR /app

# Copy package files
COPY package.json yarn.lock ./

# Install dependencies
RUN yarn install --frozen-lockfile

# Copy source code
COPY . .

# Build the application
RUN yarn build

# Runtime stage
FROM nginx:alpine

# Install additional packages for proper MIME type handling
RUN apk add --no-cache curl

# Copy nginx configuration
COPY docker/nginx.conf /etc/nginx/nginx.conf

# Copy built assets from builder stage
COPY --from=builder /app/index.html /usr/share/nginx/html/
COPY --from=builder /app/ui.js /usr/share/nginx/html/
COPY --from=builder /app/sw.js /usr/share/nginx/html/
COPY --from=builder /app/webmanifest.json /usr/share/nginx/html/
COPY --from=builder /app/embed.html /usr/share/nginx/html/
COPY --from=builder /app/404.html /usr/share/nginx/html/
COPY --from=builder /app/favicons /usr/share/nginx/html/favicons
COPY --from=builder /app/ruffle /usr/share/nginx/html/ruffle
COPY --from=builder /app/adblock /usr/share/nginx/html/adblock

# Add health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
    CMD curl -f http://localhost:80/ || exit 1

# Expose port
EXPOSE 80

# Start nginx
CMD ["nginx", "-g", "daemon off;"]