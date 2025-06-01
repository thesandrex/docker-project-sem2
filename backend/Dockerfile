# ----------------------
# STAGE 1: Build
# ----------------------
FROM golang:1.22-alpine AS builder

RUN apk add --no-cache git

WORKDIR /app

COPY go.mod go.sum ./
RUN go mod download

COPY . .

RUN CGO_ENABLED=0 GOOS=linux go build -o app ./cmd/api/main.go

# ----------------------
# STAGE 2: Run (non-root)
# ----------------------
FROM alpine:latest

RUN addgroup -g 1005 appgroup && adduser -u 1005 -S appuser -G appgroup

WORKDIR /app

COPY --from=builder /app/app .

RUN chown -R appuser:appgroup /app

USER appuser

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s CMD ["/app/app", "healthcheck"]

ENTRYPOINT ["./app"]
