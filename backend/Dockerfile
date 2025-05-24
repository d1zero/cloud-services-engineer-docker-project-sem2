FROM golang:1.17.13-alpine3.16 AS builder

RUN apk update --no-cache && apk add --no-cache tzdata git curl

WORKDIR /build

RUN adduser -D -g '' appuser

ADD go.mod .
ADD go.sum .

RUN go mod download

COPY . .

RUN GOOS=linux CGO_ENABLED=0 go build -ldflags="-s -w" -o /app/main ./cmd/main.go

FROM alpine:3.16

RUN addgroup -g 1001 -S appgroup && \
    adduser -u 1001 -S appuser -G appgroup

WORKDIR /app

COPY --from=builder /app/main .

USER appuser

EXPOSE 8081

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD wget -q --spider http://localhost:8081/health || exit 1

CMD ["./main"]
