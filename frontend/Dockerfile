# ----------------------
# STAGE 1: Build frontend
# ----------------------
FROM node:20-alpine as builder

WORKDIR /app

COPY package*.json ./

RUN npm ci

COPY . .

ENV NODE_OPTIONS=--openssl-legacy-provider

RUN npm run build

# ----------------------
# STAGE 2: Run nginx (non-root)
# ----------------------
FROM nginx:1.25-alpine

COPY --from=builder /app/dist /usr/share/nginx/html

# Create non-root user and set up directories
RUN addgroup -g 1006 appgroup && adduser -u 1006 -S appuser -G appgroup && \
    mkdir -p /tmp/nginx && \
    chown -R appuser:appgroup /tmp/nginx /var/cache/nginx /var/run /usr/share/nginx/html && \
    chmod -R 777 /tmp/nginx /var/cache/nginx /var/run && \
    chmod -R 755 /usr/share/nginx/html

# Create a custom nginx configuration
RUN echo 'events {' > /tmp/nginx.conf && \
    echo '    worker_connections 1024;' >> /tmp/nginx.conf && \
    echo '}' >> /tmp/nginx.conf && \
    echo 'http {' >> /tmp/nginx.conf && \
    echo '    include /etc/nginx/mime.types;' >> /tmp/nginx.conf && \
    echo '    server {' >> /tmp/nginx.conf && \
    echo '        listen 80;' >> /tmp/nginx.conf && \
    echo '        root /usr/share/nginx/html;' >> /tmp/nginx.conf && \
    echo '        location / {' >> /tmp/nginx.conf && \
    echo '            try_files $uri $uri/ /index.html;' >> /tmp/nginx.conf && \
    echo '        }' >> /tmp/nginx.conf && \
    echo '    }' >> /tmp/nginx.conf && \
    echo '}' >> /tmp/nginx.conf

USER appuser

EXPOSE 80

CMD ["nginx", "-c", "/tmp/nginx.conf", "-g", "daemon off; pid /tmp/nginx/nginx.pid;"]

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s CMD wget -q --spider http://localhost || exit 1
