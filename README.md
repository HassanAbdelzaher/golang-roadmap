# Backend Development Roadmap Using Golang

This roadmap is designed to help you become a **production-ready Go backend developer**, not only someone who knows Go syntax.

The roadmap focuses on:

- Go backend development
- REST APIs
- PostgreSQL
- Clean architecture
- Authentication
- Testing
- Docker
- Protocol Buffers
- ConnectRPC
- gRPC
- Redis
- Background jobs
- Temporal
- Microservices
- Kubernetes
- Observability
- Security

---

# 1. Go Language Foundations

Start with the Go language itself.

## Topics to Learn

- Variables and constants
- Functions
- Structs
- Interfaces
- Pointers
- Slices
- Maps
- Arrays
- Packages
- Go modules
- Error handling
- `defer`
- `panic`
- `recover`
- `context.Context`
- Formatting with `gofmt`

## Basic Example

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello Go")
}
```

## Important Commands

```bash
go mod init myapp
go run main.go
go build
go test ./...
go fmt ./...
```

## Error Handling

Go backend code depends heavily on clean error handling.

```go
if err != nil {
    return err
}
```

Avoid ignoring errors.

Bad:

```go
result, _ := doSomething()
```

Good:

```go
result, err := doSomething()
if err != nil {
    return err
}
```

---

# 2. Go Concurrency

Go is strong for backend systems because of its concurrency model.

## Topics to Learn

- Goroutines
- Channels
- Buffered channels
- Unbuffered channels
- `sync.WaitGroup`
- `sync.Mutex`
- `sync.RWMutex`
- Worker pools
- Context cancellation
- Timeouts
- Graceful shutdown

## Goroutine Example

```go
go func() {
    fmt.Println("running in background")
}()
```

## Context Timeout Example

```go
ctx, cancel := context.WithTimeout(context.Background(), 3*time.Second)
defer cancel()
```

## Why Context Matters

`context.Context` is used in:

- HTTP requests
- Database queries
- gRPC calls
- ConnectRPC handlers
- Background jobs
- Temporal activities
- External API calls

You should understand it very well.

---

# 3. HTTP Server Development

Before using frameworks, learn the standard library.

## Basic HTTP Server

```go
package main

import (
    "net/http"
)

func main() {
    http.HandleFunc("/health", func(w http.ResponseWriter, r *http.Request) {
        w.Write([]byte("OK"))
    })

    http.ListenAndServe(":8080", nil)
}
```

## Topics to Learn

- `net/http`
- Routing
- Middleware
- JSON request decoding
- JSON response encoding
- Request validation
- Error responses
- CORS
- Graceful shutdown
- Request IDs
- Logging middleware
- Authentication middleware

## Recommended Router

For production Go APIs:

```text
net/http + chi
```

Other options:

```text
Gin
Echo
Fiber
```

Recommended choice:

```text
chi
```

Because it is simple, idiomatic, and built on top of `net/http`.

---

# 4. REST API Design

REST is still very important for public APIs, admin APIs, integrations, and dashboards.

## Common REST Pattern

```http
GET    /users
GET    /users/{id}
POST   /users
PUT    /users/{id}
PATCH  /users/{id}
DELETE /users/{id}
```

## Topics to Learn

- HTTP methods
- Status codes
- JSON APIs
- Pagination
- Filtering
- Sorting
- API versioning
- Idempotency
- Error response format
- OpenAPI / Swagger
- Request validation
- Rate limiting

## Example Error Response

```json
{
  "code": "USER_NOT_FOUND",
  "message": "User not found"
}
```

## Recommended REST API Rules

- Use nouns in URLs.
- Use HTTP methods correctly.
- Return consistent error responses.
- Validate request body.
- Do not expose internal errors.
- Use pagination for list APIs.
- Use request IDs for tracing.
- Document APIs with OpenAPI.

---

# 5. Protocol Buffers, ConnectRPC, and gRPC

This section is very important for modern Go backend development, especially when building microservices, internal APIs, mobile APIs, and frontend-compatible RPC APIs.

Recommended stack:

```text
Protocol Buffers + Buf + ConnectRPC + gRPC
```

---

## 5.1 Protocol Buffers

Protocol Buffers, or protobuf, are used to define strongly typed API contracts.

Instead of defining request and response models only in Go, you define them in `.proto` files.

## Example Proto File

```proto
syntax = "proto3";

package user.v1;

option go_package = "github.com/your-org/your-project/gen/user/v1;userv1";

message User {
  string id = 1;
  string name = 2;
  string email = 3;
}

message GetUserRequest {
  string id = 1;
}

message GetUserResponse {
  User user = 1;
}

service UserService {
  rpc GetUser(GetUserRequest) returns (GetUserResponse);
}
```

## Learn Protobuf Concepts

```text
messages
fields
field numbers
enums
services
repeated fields
optional fields
nested messages
package naming
go_package option
imports
well-known types
```

## Important Protobuf Rules

```text
Never reuse field numbers.
Never rename fields carelessly.
Reserve removed field numbers.
Use clear package versioning like user.v1.
Keep request and response messages explicit.
```

## Reserved Fields Example

```proto
message User {
  reserved 4, 5;
  reserved "old_status";

  string id = 1;
  string name = 2;
  string email = 3;
}
```

## Good API Design

Good:

```proto
service CustomerService {
  rpc CreateCustomer(CreateCustomerRequest) returns (CreateCustomerResponse);
  rpc GetCustomer(GetCustomerRequest) returns (GetCustomerResponse);
  rpc ListCustomers(ListCustomersRequest) returns (ListCustomersResponse);
}
```

Avoid:

```proto
service CustomerService {
  rpc CreateCustomer(Customer) returns (Customer);
}
```

Explicit request and response messages are better for future compatibility.

---

## 5.2 Buf

Buf is used to manage protobuf files.

Buf helps with:

```text
code generation
linting
breaking-change checks
module management
standard proto structure
CI validation
```

## Recommended Proto Structure

```text
proto/
  user/v1/user.proto
  auth/v1/auth.proto
  customer/v1/customer.proto
  common/v1/pagination.proto
  common/v1/errors.proto
```

## Example `buf.yaml`

```yaml
version: v2

modules:
  - path: proto

lint:
  use:
    - STANDARD

breaking:
  use:
    - FILE
```

## Example `buf.gen.yaml`

```yaml
version: v2

plugins:
  - remote: buf.build/protocolbuffers/go
    out: gen
    opt:
      - paths=source_relative

  - remote: buf.build/connectrpc/go
    out: gen
    opt:
      - paths=source_relative
```

## Generate Code

```bash
buf generate
```

---

## 5.3 ConnectRPC

ConnectRPC gives you a clean way to build protobuf-based APIs in Go.

It supports:

```text
Connect protocol
gRPC
gRPC-Web
HTTP/1.1
HTTP/2
Browser clients
Mobile clients
Type-safe generated clients
```

ConnectRPC is very useful when you want:

```text
Go backend
Next.js frontend
React frontend
Mobile clients
Internal microservices
gRPC-style contracts
Web-compatible APIs without complex Envoy setup
```

## ConnectRPC Server Example

```go
package main

import (
    "net/http"

    userv1connect "github.com/your-org/your-project/gen/user/v1/userv1connect"
)

type UserServer struct{}

func main() {
    mux := http.NewServeMux()

    path, handler := userv1connect.NewUserServiceHandler(&UserServer{})
    mux.Handle(path, handler)

    http.ListenAndServe(":8080", mux)
}
```

## ConnectRPC with Chi

```go
r := chi.NewRouter()

path, handler := userv1connect.NewUserServiceHandler(&UserServer{})
r.Mount(path, handler)

http.ListenAndServe(":8080", r)
```

## ConnectRPC Advantages

```text
Works with browsers
Works with HTTP/1.1 and HTTP/2
Simpler than raw gRPC for web apps
Uses protobuf contracts
Supports generated clients
Easy to use with Go net/http
Good for internal and external APIs
```

---

## 5.4 gRPC

gRPC is a high-performance RPC framework commonly used for service-to-service communication.

## Learn gRPC Concepts

```text
Unary RPC
Server streaming
Client streaming
Bidirectional streaming
Deadlines
Metadata
Interceptors
Status codes
Error handling
TLS
mTLS
Reflection
Health checks
Load balancing
```

## Example Service

```proto
service UserService {
  rpc GetUser(GetUserRequest) returns (GetUserResponse);
  rpc ListUsers(ListUsersRequest) returns (ListUsersResponse);
}
```

## Typical gRPC Flow

```text
.proto file
↓
Generate Go code
↓
Implement service interface
↓
Register service on gRPC server
↓
Run server on port 9090
```

## Example Go Implementation Shape

```go
type UserServer struct {
    userv1.UnimplementedUserServiceServer
}

func (s *UserServer) GetUser(
    ctx context.Context,
    req *userv1.GetUserRequest,
) (*userv1.GetUserResponse, error) {
    return &userv1.GetUserResponse{
        User: &userv1.User{
            Id:    req.Id,
            Name:  "Hassan",
            Email: "user@example.com",
        },
    }, nil
}
```

---

## 5.5 gRPC vs REST vs ConnectRPC

Use this simple rule:

```text
REST:
Best for public HTTP APIs, simple CRUD, third-party integrations.

gRPC:
Best for internal service-to-service communication.

ConnectRPC:
Best when you want protobuf/gRPC style with browser and HTTP compatibility.
```

## Practical Recommendation

```text
External public APIs:
REST or ConnectRPC

Internal microservices:
gRPC or ConnectRPC

Frontend web apps:
ConnectRPC

Mobile apps:
ConnectRPC or gRPC

Enterprise systems:
Protobuf contracts + ConnectRPC + gRPC
```

For modern Go backend development:

```text
Start with REST
Then learn protobuf
Then ConnectRPC
Then gRPC
Then microservice communication
```

---

## 5.6 Error Handling in gRPC and ConnectRPC

Use proper gRPC status codes.

## Common Codes

```text
InvalidArgument
Unauthenticated
PermissionDenied
NotFound
AlreadyExists
FailedPrecondition
ResourceExhausted
Internal
Unavailable
DeadlineExceeded
```

## gRPC Error Example

```go
return nil, status.Error(codes.NotFound, "user not found")
```

## ConnectRPC Error Example

```go
return nil, connect.NewError(connect.CodeNotFound, errors.New("user not found"))
```

## Business Error Codes

Use business-level codes for your application.

```text
CUSTOMER_NOT_FOUND
CUSTOMER_ALREADY_EXISTS
INVALID_PHONE_NUMBER
LIMIT_EXCEEDED
KYC_REQUIRED
PAYMENT_FAILED
```

Example:

```proto
message ErrorDetail {
  string code = 1;
  string message = 2;
  map<string, string> metadata = 3;
}
```

---

## 5.7 Authentication with gRPC and ConnectRPC

For gRPC and ConnectRPC, tokens are usually sent in metadata or headers.

Example:

```text
authorization: Bearer <access_token>
```

## Common Flow

```text
Client sends Authorization header
↓
Interceptor validates token
↓
User identity added to context
↓
Handler uses identity from context
```

## Learn

```text
Unary interceptors
Stream interceptors
Auth middleware
Request ID middleware
Logging middleware
Rate limiting
Tenant ID propagation
Token validation
Context values
```

---

## 5.8 Recommended Project Structure with Proto

```text
backend/
  cmd/
    api/
      main.go

  internal/
    user/
      handler.go
      service.go
      repository.go

  proto/
    user/v1/user.proto
    auth/v1/auth.proto
    common/v1/errors.proto

  gen/
    user/v1/user.pb.go
    user/v1/user.connect.go
    auth/v1/auth.pb.go
    auth/v1/auth.connect.go

  buf.yaml
  buf.gen.yaml
  go.mod
```

For large systems, use a separate proto repository:

```text
github.com/company/proto
```

Example:

```text
github.com/next-pay/proto
```

---

## 5.9 Learning Exercises

## Exercise 1: UserService Proto

Create:

```text
proto/user/v1/user.proto
```

Implement:

```text
CreateUser
GetUser
ListUsers
UpdateUser
DeleteUser
```

Generate Go code using Buf.

---

## Exercise 2: ConnectRPC Server

Build a Go server exposing:

```text
UserService
AuthService
HealthService
```

Use:

```text
connect-go
chi
slog
PostgreSQL
```

---

## Exercise 3: Next.js Client

Generate a TypeScript client and call your Go ConnectRPC backend from Next.js.

Learn:

```text
Connect Web client
CORS
CSRF headers
Authorization headers
Server-side proxying
```

---

## Exercise 4: Microservices Communication

Create two services:

```text
customer-service
order-service
```

The `order-service` calls `customer-service` using gRPC or ConnectRPC.

Learn:

```text
timeouts
retries
deadlines
metadata
request IDs
error mapping
```

---

## 5.10 Production Checklist

Before using protobuf, gRPC, or ConnectRPC in production, understand:

```text
Proto versioning
Backward compatibility
Buf linting
Buf breaking checks
Request validation
Deadlines and timeouts
Interceptors
Authentication metadata
TLS
mTLS
Health checks
Reflection
OpenTelemetry tracing
Structured logging
Error mapping
Generated client packages
CI pipeline for proto generation
```

---

# 6. Database Development

Start with PostgreSQL.

## SQL Topics to Learn

- Tables
- Columns
- Data types
- Primary keys
- Foreign keys
- Unique constraints
- Check constraints
- Indexes
- Joins
- Transactions
- Isolation levels
- Views
- Stored procedures basics
- Query optimization
- Execution plans

## Recommended Go Database Stack

For serious backend development:

```text
PostgreSQL + pgx + sqlc
```

## Other Options

```text
GORM
sqlx
database/sql
ent
bun
```

## Recommendation

Beginner-friendly:

```text
GORM
```

Production-friendly:

```text
pgx + sqlc
```

## SQLC Example

SQL file:

```sql
-- name: GetUser :one
SELECT id, name, email
FROM users
WHERE id = $1;
```

Generated Go usage:

```go
user, err := queries.GetUser(ctx, userID)
if err != nil {
    return err
}
```

## Learn Database Migrations

Recommended tools:

```text
golang-migrate
goose
atlas
```

Example migration:

```sql
CREATE TABLE users (
    id UUID PRIMARY KEY,
    name TEXT NOT NULL,
    email TEXT NOT NULL UNIQUE,
    created_at TIMESTAMPTZ NOT NULL DEFAULT now()
);
```

---

# 7. Authentication and Authorization

Authentication is one of the most important backend skills.

## Topics to Learn

- Password hashing
- bcrypt
- argon2
- JWT access tokens
- Refresh tokens
- Session management
- Token rotation
- Role-based access control
- Permission-based access control
- API keys
- OAuth2 basics
- Device/session revocation
- Rate limiting
- Account lockout

## Basic Auth Flow

```text
User login
↓
Validate password
↓
Create access token
↓
Create refresh token
↓
Return tokens
```

## Production Auth Flow

```text
User login
↓
Validate credentials
↓
Create short-lived access token
↓
Create long-lived refresh token
↓
Store refresh token hash
↓
Return tokens
↓
Rotate refresh token on every refresh
```

## Security Rules

```text
Never store raw passwords.
Never store raw refresh tokens.
Never log tokens.
Never log passwords.
Never expose internal auth errors.
```

## Recommended Token Strategy

```text
Access token:
Short-lived, for example 5 to 15 minutes.

Refresh token:
Longer-lived, stored as hash in database.

Refresh rotation:
Every refresh invalidates old refresh token and creates a new one.
```

---

# 8. Clean Architecture

Clean architecture helps keep your backend maintainable.

## Basic Layers

```text
Handler / Controller
↓
Service / Usecase
↓
Repository
↓
Database
```

## Example Structure

```text
myapp/
  cmd/
    api/
      main.go

  internal/
    config/
    http/
    middleware/
    user/
      handler.go
      service.go
      repository.go
      model.go

  migrations/
  sql/
  go.mod
```

## Rules

- Do not put business logic in HTTP handlers.
- Keep handlers focused on request and response.
- Put business rules in services.
- Put database logic in repositories.
- Use interfaces when they help testing or decoupling.
- Avoid over-engineering early.

## Example Flow

```text
HTTP request
↓
UserHandler
↓
UserService
↓
UserRepository
↓
PostgreSQL
```

---

# 9. Configuration and Environment

Backend systems need clean configuration.

## Learn

- Environment variables
- `.env` files
- Config structs
- Validation
- Secret management
- Dev/staging/prod environments

## Example `.env`

```bash
APP_PORT=8080
DATABASE_URL=postgres://user:pass@localhost:5432/app
JWT_SECRET=secret
REDIS_URL=redis://localhost:6379
```

## Production Secrets

Use:

```text
Vault
GCP Secret Manager
AWS Secrets Manager
Azure Key Vault
Kubernetes Secrets
```

Do not commit secrets to Git.

---

# 10. Logging and Observability

Production backend systems need observability from day one.

## Learn

- Structured logging
- Metrics
- Tracing
- Request IDs
- Correlation IDs
- Audit logs
- Health checks
- Readiness checks
- Prometheus
- Grafana
- OpenTelemetry

## Recommended Logging Libraries

```text
slog
zap
zerolog
```

## Example Structured Log

```go
logger.Info("user created", "user_id", user.ID)
```

## Observability Stack

```text
Logs:
slog / zap / zerolog

Metrics:
Prometheus

Dashboards:
Grafana

Tracing:
OpenTelemetry

Health checks:
HTTP /healthz and /readyz
```

---

# 11. Testing

Testing is essential for professional backend development.

## Learn

- Unit tests
- Integration tests
- Repository tests
- API tests
- Table-driven tests
- Mocking
- Test containers
- HTTP testing
- gRPC testing

## Run Tests

```bash
go test ./...
```

## Basic Test Example

```go
func TestAdd(t *testing.T) {
    got := Add(2, 3)
    want := 5

    if got != want {
        t.Fatalf("got %d, want %d", got, want)
    }
}
```

## Useful Tools

```text
testing
testify
gomock
mockery
httptest
testcontainers-go
```

## Testing Priorities

Start with:

```text
Service tests
Repository tests
Handler tests
Authentication tests
Integration tests
```

---

# 12. Docker

Docker is required for modern backend development.

## Learn

- Dockerfile
- Docker Compose
- Multi-stage builds
- Container networking
- Volumes
- Health checks
- Environment variables
- Image tags
- Registry push/pull

## Example Dockerfile

```dockerfile
FROM golang:1.24-alpine AS builder

WORKDIR /app

COPY go.mod go.sum ./
RUN go mod download

COPY . .
RUN go build -o server ./cmd/api

FROM alpine:latest

WORKDIR /app
COPY --from=builder /app/server .

EXPOSE 8080

CMD ["./server"]
```

## Example Docker Compose

```yaml
services:
  api:
    build: .
    ports:
      - "8080:8080"
    environment:
      DATABASE_URL: postgres://app:app@postgres:5432/app?sslmode=disable
    depends_on:
      - postgres

  postgres:
    image: postgres:17
    environment:
      POSTGRES_USER: app
      POSTGRES_PASSWORD: app
      POSTGRES_DB: app
    ports:
      - "5432:5432"
```

---

# 13. Redis, Caching, and Background Jobs

Redis is commonly used in backend systems.

## Use Cases

```text
Caching
Rate limiting
Session storage
Distributed locks
Queues
Temporary tokens
OTP storage
Idempotency keys
```

## Learn

- Redis strings
- Redis hashes
- Expiration
- TTL
- Atomic operations
- Lua scripts basics
- Rate limiting
- Distributed locks
- Cache invalidation

## Cache Pattern

```text
Request data
↓
Check Redis cache
↓
If found, return cached data
↓
If not found, query database
↓
Store result in Redis
↓
Return data
```

## Background Job Pattern

```text
API receives request
↓
Store request in database
↓
Publish job/event
↓
Worker processes job
↓
Update database
```

---

# 14. Messaging and Event-Driven Systems

Learn async communication after you understand APIs and databases.

## Tools

```text
Redis Queue
NATS
RabbitMQ
Kafka
Google Pub/Sub
Temporal
```

## When to Use What

```text
Redis:
Simple queues, cache, rate limits.

RabbitMQ:
Traditional message queue.

NATS:
Lightweight messaging.

Kafka:
High-volume event streaming.

Google Pub/Sub:
Managed cloud messaging.

Temporal:
Durable workflow orchestration.
```

## Event Example

```json
{
  "event_id": "evt_123",
  "event_type": "ORDER_CREATED",
  "occurred_at": "2026-01-01T10:00:00Z",
  "data": {
    "order_id": "ord_123"
  }
}
```

## Important Concepts

```text
At-least-once delivery
Idempotency
Retries
Dead-letter queues
Ordering
Event versioning
Outbox pattern
Saga pattern
```

---

# 15. Temporal Workflow Orchestration

Temporal is powerful for long-running workflows.

Use Temporal when business processes need:

```text
Retries
Timeouts
Human approval
Long-running flows
Compensation
Saga pattern
Durable execution
Failure recovery
```

## Example Use Cases

```text
Order processing
Payment workflow
KYC workflow
Customer onboarding
Notification workflow
BNPL approval flow
Document processing pipeline
```

## Temporal Concepts

```text
Workflow
Activity
Worker
Task Queue
Signal
Query
Timer
Retry Policy
Child Workflow
Continue-As-New
```

## Recommended Rule

```text
Workflows orchestrate.
Activities do real work.
Services own business logic.
```

## Example Flow

```text
Start onboarding workflow
↓
Verify phone OTP
↓
Run KYC check
↓
Evaluate risk
↓
Create customer
↓
Create wallet/account
↓
Send notification
```

---

# 16. Microservices

Do not start with microservices too early.

First master:

```text
Go
REST
PostgreSQL
Clean architecture
Testing
Docker
```

Then move to microservices.

## Learn

- Service boundaries
- API gateway
- gRPC communication
- ConnectRPC communication
- Event-driven communication
- Distributed transactions
- Saga pattern
- Idempotency
- Retry policy
- Circuit breaker
- Rate limiting
- Service discovery
- Observability
- Centralized logging

## Example Services

```text
auth-service
customer-service
order-service
payment-service
notification-service
workflow-service
```

## Communication Types

```text
Synchronous:
REST, gRPC, ConnectRPC

Asynchronous:
Kafka, Pub/Sub, NATS, RabbitMQ

Durable workflows:
Temporal
```

## Microservice Rule

```text
Each service should own its database.
Services should communicate through APIs or events.
Avoid sharing database tables between services.
```

---

# 17. Security

Security is mandatory for backend development.

## Topics to Learn

- HTTPS / TLS
- mTLS
- Input validation
- SQL injection prevention
- XSS basics
- CSRF basics
- CORS
- JWT security
- Password hashing
- Rate limiting
- Secrets management
- Audit logs
- Secure headers
- Dependency scanning
- Supply-chain security
- Least privilege
- Encryption at rest
- Encryption in transit

## Important Rule

```text
Never log passwords, tokens, national IDs, credit card numbers, or sensitive customer data.
```

## API Security Checklist

```text
Use HTTPS.
Validate all input.
Use prepared SQL queries.
Hash passwords.
Rotate refresh tokens.
Rate limit login and OTP endpoints.
Use short-lived access tokens.
Store secrets outside code.
Use audit logs.
Return safe error messages.
```

---

# 18. Deployment

Learn deployment gradually.

## Start Simple

```text
Linux VPS
Docker Compose
Nginx
PostgreSQL
Systemd
```

## Then Learn Cloud

```text
Cloud Run
GKE
AWS ECS
AWS EKS
Azure Container Apps
Kubernetes
```

## Then Learn Kubernetes

```text
Pods
Deployments
Services
Ingress
ConfigMaps
Secrets
Horizontal Pod Autoscaler
Helm
Namespaces
Persistent Volumes
```

## Go Deployment Options

```text
Small project:
Docker Compose on VPS

Scalable API:
Cloud Run

Enterprise platform:
Kubernetes / GKE

Internal tool:
Linux service with systemd
```

---

# 19. CI/CD

CI/CD automates testing and deployment.

## Learn

- GitHub Actions
- Build pipeline
- Test pipeline
- Docker image build
- Push image to registry
- Deploy to server/cloud
- Database migration step
- Environment-specific configuration

## Simple Pipeline

```text
Push code
↓
Run tests
↓
Build Docker image
↓
Push image
↓
Run migrations
↓
Deploy
```

## Example Pipeline Tasks

```text
go fmt check
go vet
go test ./...
buf lint
buf breaking
docker build
docker push
deploy
```

---

# 20. Kubernetes and Cloud Native Backend

After Docker and deployment basics, learn Kubernetes.

## Learn

- Kubernetes architecture
- Pods
- Deployments
- Services
- Ingress
- ConfigMaps
- Secrets
- Jobs
- CronJobs
- Volumes
- Helm
- Autoscaling
- Rolling updates
- Health probes
- Resource limits
- Namespaces

## Advanced Topics

```text
Istio
Kong Gateway
NGINX Ingress
cert-manager
mTLS
Service mesh
Network policies
Prometheus Operator
Grafana dashboards
OpenTelemetry collector
```

## Production Kubernetes Skills

```text
Use readiness and liveness probes.
Set CPU and memory limits.
Use horizontal autoscaling.
Use rolling deployments.
Use secrets safely.
Monitor services.
Centralize logs.
Secure ingress.
```

---

# 21. Advanced Backend Topics

After the basics, study advanced topics.

## Topics

```text
Caching strategies
Distributed locks
WebSockets
Server-Sent Events
File uploads
Object storage
Search engines
Elasticsearch
Meilisearch
Payment integrations
Webhooks
Multi-tenancy
Rate limiting
Feature flags
Audit logging
Domain-driven design
CQRS
Event sourcing
Outbox pattern
Saga pattern
Workflow orchestration
```

---

# 22. Recommended Learning Order

Follow this order:

```text
1. Go basics
2. HTTP APIs
3. REST API design
4. PostgreSQL and SQL
5. Clean architecture
6. Authentication
7. Testing
8. Docker
9. Protocol Buffers
10. Buf
11. ConnectRPC
12. gRPC
13. Redis
14. Background workers
15. Messaging
16. Temporal
17. Microservices
18. Kubernetes
19. Observability
20. Security and production hardening
21. CI/CD
22. Cloud deployment
```

---

# 23. Recommended Go Backend Stack

For modern production backend development:

```text
Language:
Go

HTTP Router:
chi

Database:
PostgreSQL

DB Driver:
pgx

Query Tool:
sqlc

Migrations:
goose or golang-migrate

Cache:
Redis

Messaging:
NATS / Kafka / Google Pub/Sub

Workflow:
Temporal

API:
REST + ConnectRPC + gRPC

Proto:
Protocol Buffers + Buf

Auth:
JWT + refresh token rotation

Logging:
slog or zap

Testing:
testing + testify + testcontainers-go

Docs:
OpenAPI / Swagger

Deployment:
Docker + Kubernetes / Cloud Run

Monitoring:
Prometheus + Grafana + OpenTelemetry
```

---

# 24. Project Roadmap

## Project 1: Task API

Build a simple task management API.

## Features

```text
Create task
Update task
Delete task
List tasks
Mark task as complete
```

## Tech

```text
Go
Chi
PostgreSQL
Docker
```

---

## Project 2: Auth Service

Build a production-style authentication service.

## Features

```text
Register
Login
Refresh token
Logout
Change password
Role permissions
Session management
```

## Tech

```text
Go
PostgreSQL
Redis
JWT
bcrypt
Docker
```

---

## Project 3: E-commerce Backend

Build an e-commerce backend.

## Features

```text
Products
Customers
Orders
Payments
Inventory
Notifications
Admin APIs
```

## Tech

```text
Go
PostgreSQL
Redis
Background workers
Webhooks
OpenAPI
Docker Compose
```

---

## Project 4: ConnectRPC Backend

Build a protobuf-based backend.

## Services

```text
UserService
AuthService
ProductService
OrderService
```

## Tech

```text
Go
Protocol Buffers
Buf
ConnectRPC
PostgreSQL
sqlc
chi
```

---

## Project 5: gRPC Microservices

Build two or three services that communicate using gRPC.

## Services

```text
customer-service
order-service
notification-service
```

## Learn

```text
gRPC clients
gRPC servers
Deadlines
Metadata
Interceptors
Error handling
Service-to-service authentication
```

---

## Project 6: Workflow-Based Backend

Build a workflow-based order system.

## Features

```text
Order workflow
Payment workflow
Notification workflow
Retry failed tasks
Audit events
Human approval step
```

## Tech

```text
Go
Temporal
PostgreSQL
Redis
gRPC
ConnectRPC
```

---

## Project 7: Microservices Platform

Build a full microservice platform.

## Services

```text
api-gateway
auth-service
customer-service
order-service
payment-service
notification-service
workflow-service
```

## Tech

```text
Go
gRPC
ConnectRPC
Protocol Buffers
Buf
Temporal
PostgreSQL
Redis
Docker Compose
Kubernetes
Prometheus
Grafana
OpenTelemetry
```

---

# 25. Six-Month Study Plan

## Month 1: Go and HTTP

Learn:

```text
Go syntax
Structs
Interfaces
Errors
Context
HTTP server
JSON APIs
```

Build:

```text
Simple REST API
Task API
Health check endpoint
```

---

## Month 2: PostgreSQL and Clean Architecture

Learn:

```text
PostgreSQL
SQL
Indexes
Transactions
sqlc
Migrations
Clean architecture
```

Build:

```text
CRUD API with PostgreSQL
Repository layer
Service layer
Migration setup
```

---

## Month 3: Authentication, Redis, and Testing

Learn:

```text
JWT
Refresh tokens
Password hashing
Redis
Rate limiting
Unit testing
Integration testing
```

Build:

```text
Auth service
Login/logout APIs
Refresh token rotation
Role-based access
```

---

## Month 4: Protobuf, Buf, ConnectRPC, and gRPC

Learn:

```text
Protocol Buffers
Buf
ConnectRPC
gRPC
Interceptors
Deadlines
Metadata
Generated clients
```

Build:

```text
UserService using ConnectRPC
AuthService using ConnectRPC
Service-to-service call using gRPC
```

---

## Month 5: Background Jobs, Messaging, and Temporal

Learn:

```text
Redis queues
NATS or Kafka
Event-driven architecture
Temporal workflows
Saga pattern
Outbox pattern
```

Build:

```text
Order workflow
Payment workflow
Notification worker
Event publisher
```

---

## Month 6: Kubernetes, Observability, and Production

Learn:

```text
Docker production builds
Kubernetes
Helm
Prometheus
Grafana
OpenTelemetry
CI/CD
Security hardening
```

Build:

```text
Deploy microservices to Kubernetes
Add monitoring dashboards
Add tracing
Add CI/CD pipeline
```

---

# 26. Final Target

After this roadmap, you should be able to build:

```text
Production Go backend
REST APIs
ConnectRPC APIs
gRPC microservices
Protocol Buffer contracts
PostgreSQL repositories
Redis cache
Background workers
Temporal workflows
Dockerized services
Kubernetes-ready deployments
Observable systems
Secure authentication systems
CI/CD pipelines
```

---

# 27. Best Stack for Enterprise Go Backend

For your backend direction, the strongest stack is:

```text
Go
PostgreSQL
pgx
sqlc
chi
Protocol Buffers
Buf
ConnectRPC
gRPC
Redis
Temporal
Docker
Kubernetes
Prometheus
Grafana
OpenTelemetry
```

This stack is excellent for:

```text
Enterprise backend systems
Workflow engines
BNPL platforms
Payment systems
Microservices
Internal platforms
Data processing systems
Document processing systems
```
