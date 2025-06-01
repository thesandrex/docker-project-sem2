# ----------------------
# STAGE 1: Build
# ----------------------
FROM golang:1.22-alpine AS builder

RUN apk add --no-cache git

WORKDIR /app

COPY go.mod go.sum ./
RUN go mod download

COPY . .

RUN CGO_ENABLED=0 GOOS=linux go build -o app ./cmd/api

# ----------------------
# STAGE 2: Run (non-root)
# ----------------------
FROM alpine:latest

RUN apk add --no-cache curl

RUN addgroup -g 1005 appgroup && adduser -u 1005 -S appuser -G appgroup

WORKDIR /app

COPY --from=builder /app/app .

RUN chown -R appuser:appgroup /app

USER appuser

ENTRYPOINT ["./app"]

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -fIs http://localhost:8081/health
