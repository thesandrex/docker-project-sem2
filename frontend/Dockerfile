# ----------------------
# STAGE 1: Build
# ----------------------
FROM node:20-alpine AS builder

WORKDIR /app

COPY package*.json ./

RUN npm ci

COPY . .

ENV NODE_OPTIONS=--openssl-legacy-provider

RUN npm run build

# ----------------------
# STAGE 2: Run with nginx
# ----------------------
FROM nginx:1.25-alpine

RUN rm -rf /usr/share/nginx/html/*

COPY --from=builder /app/dist /usr/share/nginx/html

RUN addgroup -g 1006 appgroup && adduser -u 1006 -S appuser -G appgroup

RUN chown -R appuser:appgroup /usr/share/nginx/html

USER appuser

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s CMD wget -q --spider http://localhost || exit 1
