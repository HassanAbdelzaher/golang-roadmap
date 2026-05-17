# The Professional Go Roadmap — Beginner to Expert

A step-by-step path to becoming a **production-grade Go engineer**, not just someone who knows Go syntax.

This roadmap is organized into **5 levels**. Each level has a clear goal, the
topics to learn, practice projects, and a **mastery checklist** that gates
progression. Do not skip levels — each one assumes the previous is solid.

---

## How to Use This Roadmap

- Work **top to bottom**. Levels build on each other.
- For every level, build the project(s) before moving on.
- Treat the **"You've mastered this level when…"** checklist as a gate, not a suggestion.
- Read code, not just tutorials. Read the Go standard library.
- Write Go every day. Reading about Go does not make you good at Go.

### The 5 Levels at a Glance

| Level | Name | Focus | Typical Time* |
|-------|------|-------|---------------|
| 1 | Foundations | Syntax, types, tooling, first programs | 2–4 weeks |
| 2 | Idiomatic Go | Interfaces, errors, generics, stdlib, testing | 4–6 weeks |
| 3 | Concurrency & Services | Goroutines, HTTP, databases, architecture | 6–10 weeks |
| 4 | Production Systems | RPC, messaging, observability, security, deployment | 8–12 weeks |
| 5 | Mastery | Runtime, performance, internals, distributed systems | Ongoing |

\* *Assumes consistent daily practice. Move on by competence, not by calendar.*

### Target Stack (where this roadmap leads)

```text
Language:     Go
HTTP Router:  net/http + chi
Database:     PostgreSQL + pgx + sqlc
Migrations:   goose or golang-migrate
Cache/Queue:  Redis
Messaging:    NATS / Kafka / Google Pub/Sub
Workflows:    Temporal
APIs:         REST + Protocol Buffers + Buf + ConnectRPC + gRPC
Auth:         JWT + refresh-token rotation
Logging:      slog
Testing:      testing + testify + testcontainers-go
Deployment:   Docker + Kubernetes / Cloud Run
Observability: Prometheus + Grafana + OpenTelemetry
```

---

# Level 1 — Foundations (Beginner)

**Goal:** Write, build, and run correct Go programs. Be fluent with the toolchain.

## 1.1 Setup & Tooling

- Install Go (use the latest stable release)
- `GOROOT`, `GOPATH`, the module cache
- Editor setup (VS Code + `gopls`, or GoLand)
- The `go` command:

```bash
go mod init example.com/myapp   # start a module
go run .                        # run
go build ./...                  # build everything
go test ./...                   # test everything
go fmt ./...                    # format (non-negotiable)
go vet ./...                    # static checks
go doc fmt.Println              # read docs from the terminal
```

## 1.2 Language Basics

- Variables, constants, `iota`
- Basic types, zero values, type conversions
- Control flow: `if`, `for`, `switch`, labels
- Functions, multiple return values, named returns, variadics
- `defer` execution order
- Arrays vs **slices** (length, capacity, `append`, slicing, aliasing)
- **Maps** (zero value, comma-ok, deletion, iteration order)
- Strings, runes, bytes, `[]byte` ↔ `string`
- Structs, struct embedding, struct tags
- Pointers (and why Go has no pointer arithmetic)
- Packages, exported vs unexported, `go.mod` / `go.sum`

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, Go")
}
```

## 1.3 Errors From Day One

Go's error handling is explicit. Learn it correctly now — bad habits are hard to unlearn.

```go
// Bad — silently swallows failures.
result, _ := doSomething()

// Good — handle or propagate.
result, err := doSomething()
if err != nil {
    return err
}
```

## Practice

- A CLI that reverses a string and counts words.
- A number-guessing game.
- A program that reads a file and prints line counts.

## ✅ Level 1 Mastery Checklist

Move on when you can:

- [ ] Explain the difference between a slice and an array, and predict aliasing bugs.
- [ ] Build and run a multi-file module without help.
- [ ] Explain why `result, _ :=` is dangerous.
- [ ] Use `go doc` instead of reaching for a browser.
- [ ] Write a 100-line program with zero `go vet` warnings.

---

# Level 2 — Idiomatic Go (Advanced Beginner)

**Goal:** Write Go that a senior Go engineer would approve in code review.

## 2.1 Interfaces & Composition

- Implicit interface satisfaction ("accept interfaces, return structs")
- Small interfaces (`io.Reader`, `io.Writer`, `error`, `fmt.Stringer`)
- Interface values: `(type, value)`, the `nil`-interface pitfall
- Type assertions and type switches
- Composition over inheritance (struct & interface embedding)

## 2.2 Errors, Properly

- The `error` interface and sentinel errors (`errors.Is`)
- Custom error types and `errors.As`
- Wrapping with `fmt.Errorf("...: %w", err)`
- `panic` / `recover` — and why you almost never use them for control flow
- Designing error contracts for a package's callers

```go
var ErrNotFound = errors.New("not found")

func GetUser(id string) (*User, error) {
    u, err := db.Find(id)
    if err != nil {
        return nil, fmt.Errorf("get user %s: %w", id, err)
    }
    return u, nil
}

// Caller:
if errors.Is(err, ErrNotFound) { ... }
```

## 2.3 Generics

- Type parameters, constraints, the `constraints` package
- When generics help (containers, algorithms) and when they hurt (over-abstraction)
- Generic functions vs generic types
- Inference and its limits

## 2.4 Standard Library Fluency

- `fmt`, `strings`, `strconv`, `bytes`, `bufio`
- `encoding/json` (marshal/unmarshal, tags, `omitempty`, custom marshalers)
- `time` (durations, monotonic clock, formatting, time zones)
- `io` / `os` (readers, writers, files, `io.Copy`)
- `sort`, `slices`, `maps` (modern generic helpers)

## 2.5 Testing & Project Layout

- `testing` package, table-driven tests, subtests (`t.Run`)
- Test helpers, golden files, `testdata/`
- `go test -run`, `-v`, `-cover`, `-count=1`
- Benchmarks (`testing.B`) and `go test -bench`
- Standard project layout (`cmd/`, `internal/`, `pkg/`)
- The Go Proverbs and *Effective Go*

```go
func TestAdd(t *testing.T) {
    cases := []struct {
        name     string
        a, b, want int
    }{
        {"positive", 2, 3, 5},
        {"zero", 0, 0, 0},
        {"negative", -1, -1, -2},
    }
    for _, tc := range cases {
        t.Run(tc.name, func(t *testing.T) {
            if got := Add(tc.a, tc.b); got != tc.want {
                t.Fatalf("Add(%d,%d) = %d, want %d", tc.a, tc.b, got, tc.want)
            }
        })
    }
}
```

## Practice

- A JSON-driven CLI (parse config, validate, pretty-print).
- A generic, well-tested `Set[T]` and `Stack[T]`.
- A small library with a clean public API, godoc comments, and ≥80% coverage.

## ✅ Level 2 Mastery Checklist

Move on when you can:

- [ ] Explain the `nil`-interface gotcha and produce it on demand.
- [ ] Choose between sentinel errors, error types, and wrapping — and justify it.
- [ ] Write table-driven tests with subtests by reflex.
- [ ] Read and explain a non-trivial file from the Go standard library.
- [ ] Decide *not* to use generics when they add no value.

---

# Level 3 — Concurrency & Services (Intermediate)

**Goal:** Build correct concurrent programs and ship a real HTTP service backed by a database.

## 3.1 Concurrency

- Goroutines and the scheduler model (GOMAXPROCS, M:N)
- Channels: unbuffered vs buffered, directionality, closing, `range`
- `select`, timeouts, `default`
- `sync`: `WaitGroup`, `Mutex`, `RWMutex`, `Once`, `Pool`
- `sync/atomic` and when a mutex is clearer
- `context.Context`: cancellation, deadlines, values, propagation
- Patterns: worker pool, fan-in/fan-out, pipeline, bounded concurrency
- Graceful shutdown
- The race detector: `go test -race`, `go run -race`

```go
ctx, cancel := context.WithTimeout(context.Background(), 3*time.Second)
defer cancel()

results := make(chan int)
var wg sync.WaitGroup
for _, job := range jobs {
    wg.Add(1)
    go func(j Job) {
        defer wg.Done()
        select {
        case results <- process(j):
        case <-ctx.Done():
        }
    }(job)
}
go func() { wg.Wait(); close(results) }()
```

**`context.Context` shows up everywhere** — HTTP handlers, DB queries, gRPC/ConnectRPC,
background jobs, Temporal activities, external calls. Understand it deeply.

## 3.2 HTTP Servers

Learn the standard library *before* a framework.

```go
func main() {
    mux := http.NewServeMux()
    mux.HandleFunc("GET /healthz", func(w http.ResponseWriter, r *http.Request) {
        w.Write([]byte("ok"))
    })

    srv := &http.Server{Addr: ":8080", Handler: mux}
    log.Fatal(srv.ListenAndServe())
}
```

- Routing, middleware chains, request context
- JSON decode/encode, request validation, consistent error responses
- CORS, request IDs, logging & auth middleware
- Graceful shutdown (`http.Server.Shutdown`)
- Router choice: **`net/http` + `chi`** (idiomatic, stdlib-compatible).
  Alternatives: Gin, Echo, Fiber.

## 3.3 REST API Design

```http
GET    /users        GET    /users/{id}
POST   /users         PATCH  /users/{id}
PUT    /users/{id}    DELETE /users/{id}
```

- Correct HTTP methods & status codes
- Pagination, filtering, sorting, API versioning
- Idempotency, rate limiting
- Consistent error envelope; never leak internal errors
- Document with OpenAPI / Swagger

```json
{ "code": "USER_NOT_FOUND", "message": "User not found" }
```

## 3.4 Databases (PostgreSQL)

- SQL: keys, constraints, indexes, joins, transactions, isolation levels
- Query plans (`EXPLAIN ANALYZE`) and basic optimization
- **Production stack: PostgreSQL + `pgx` + `sqlc`** (beginner-friendly: GORM)
- Migrations: `goose`, `golang-migrate`, or `atlas`

```sql
-- name: GetUser :one
SELECT id, name, email FROM users WHERE id = $1;
```

```go
user, err := queries.GetUser(ctx, userID)
if err != nil {
    return fmt.Errorf("get user: %w", err)
}
```

## 3.5 Clean Architecture

```text
Handler / Controller  →  Service / Usecase  →  Repository  →  Database
```

- Keep business logic out of HTTP handlers
- Repositories own data access; services own rules
- Use interfaces where they aid testing or decoupling — not everywhere
- Avoid premature abstraction

## 3.6 Authentication & Authorization

- Password hashing (`bcrypt`, `argon2`) — never store raw passwords
- JWT access tokens + refresh tokens, **refresh-token rotation**
- Store only token *hashes*; never log tokens or passwords
- RBAC / permission-based access, API keys, OAuth2 basics
- Rate-limit login & OTP endpoints; account lockout

## 3.7 Testing Strategy

- Unit (services), integration (repositories with `testcontainers-go`), API (`httptest`)
- Mocking with `gomock` / `mockery`
- Test the layers that hold business value first

## 3.8 Docker

```dockerfile
FROM golang:1-alpine AS build
WORKDIR /src
COPY go.mod go.sum ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 go build -o /bin/server ./cmd/api

FROM gcr.io/distroless/static
COPY --from=build /bin/server /server
EXPOSE 8080
ENTRYPOINT ["/server"]
```

- Multi-stage builds, small base images, Docker Compose for local deps

## Practice — Project: Task API → Auth Service

1. **Task API:** chi + PostgreSQL + Docker. CRUD, pagination, graceful shutdown.
2. **Auth Service:** register/login/refresh/logout, RBAC, token rotation, Redis, integration tests.

## ✅ Level 3 Mastery Checklist

Move on when you can:

- [ ] Build a concurrent program that passes `-race` and shuts down cleanly.
- [ ] Explain when to use a channel vs a mutex vs an atomic.
- [ ] Ship a chi + PostgreSQL service with migrations and integration tests.
- [ ] Implement refresh-token rotation correctly.
- [ ] Justify your architecture layering in a code review.

---

# Level 4 — Production Systems (Advanced)

**Goal:** Build, secure, observe, and deploy systems that survive real traffic.

## 4.1 Protocol Buffers, Buf, ConnectRPC & gRPC

Recommended stack: **Protocol Buffers + Buf + ConnectRPC + gRPC**.

**Protobuf** — strongly-typed API contracts:

```proto
syntax = "proto3";
package user.v1;
option go_package = "example.com/gen/user/v1;userv1";

message User { string id = 1; string name = 2; string email = 3; }
message GetUserRequest { string id = 1; }
message GetUserResponse { User user = 1; }

service UserService {
  rpc GetUser(GetUserRequest) returns (GetUserResponse);
}
```

Rules: never reuse/rename field numbers, `reserved` removed fields, version
packages (`user.v1`), explicit request/response messages.

**Buf** — manage protos (codegen, lint, breaking-change detection, CI):

```yaml
# buf.yaml
version: v2
modules: [{ path: proto }]
lint: { use: [STANDARD] }
breaking: { use: [FILE] }
```

```bash
buf lint && buf breaking && buf generate
```

**ConnectRPC** — protobuf APIs that work over HTTP/1.1, HTTP/2, gRPC, and
gRPC-Web (browser-friendly, mounts on `net/http`/chi):

```go
mux := http.NewServeMux()
path, handler := userv1connect.NewUserServiceHandler(&UserServer{})
mux.Handle(path, handler)
http.ListenAndServe(":8080", mux)
```

**gRPC** — high-performance service-to-service RPC. Learn: unary & streaming,
deadlines, metadata, interceptors, status codes, TLS/mTLS, reflection, health checks.

**Choosing:**

```text
REST        → public HTTP APIs, simple CRUD, third-party integrations
gRPC        → internal service-to-service
ConnectRPC  → browser/mobile + protobuf contracts without an Envoy proxy
```

Map errors to proper codes:

```go
// gRPC
return nil, status.Error(codes.NotFound, "user not found")
// Connect
return nil, connect.NewError(connect.CodeNotFound, errors.New("user not found"))
```

## 4.2 Redis, Caching & Background Jobs

- Use cases: cache, rate limiting, sessions, distributed locks, queues, OTP, idempotency keys
- TTL, atomic ops, Lua scripts, cache invalidation, stampede protection
- Cache-aside pattern; background-job pattern (persist → enqueue → worker → update)

## 4.3 Messaging & Event-Driven Systems

- Redis Streams, NATS, RabbitMQ, Kafka, Google Pub/Sub
- At-least-once delivery, **idempotency**, retries, DLQs, ordering
- Event versioning, **Outbox** pattern, **Saga** pattern

```json
{ "event_id": "evt_123", "event_type": "ORDER_CREATED",
  "occurred_at": "2026-01-01T10:00:00Z", "data": { "order_id": "ord_123" } }
```

## 4.4 Temporal — Durable Workflows

Use when processes need retries, timeouts, human approval, compensation, or
long-running durable execution.

```text
Workflow · Activity · Worker · Task Queue · Signal · Query
Timer · Retry Policy · Child Workflow · Continue-As-New

Rule: Workflows orchestrate. Activities do work. Services own business logic.
```

## 4.5 Configuration & Secrets

- Env vars, typed config structs, validation, per-environment config
- Secrets in Vault / cloud secret managers / K8s Secrets — **never in Git**

## 4.6 Logging & Observability

- Structured logging with `slog` (alternatives: `zap`, `zerolog`)
- Metrics: Prometheus · Dashboards: Grafana · Tracing: OpenTelemetry
- Request/correlation IDs, audit logs, `/healthz` & `/readyz`

```go
slog.Info("user created", "user_id", user.ID, "tenant", tenantID)
```

## 4.7 Security

- HTTPS/TLS, mTLS, input validation, parameterized SQL
- CORS/CSRF/XSS basics, secure headers, JWT security
- Dependency & supply-chain scanning (`govulncheck`), least privilege
- Encryption at rest and in transit
- **Never log** passwords, tokens, national IDs, or card numbers

## 4.8 Deployment & CI/CD

- Start simple (VPS + Docker Compose + Nginx + systemd)
- Then Cloud Run / ECS / Kubernetes
- GitHub Actions pipeline:

```text
push → fmt/vet → go test ./... -race → buf lint/breaking
     → docker build → push → migrate → deploy
```

## Practice — Projects

- **E-commerce backend:** products, orders, payments, webhooks, background workers, OpenAPI.
- **ConnectRPC backend:** User/Auth/Product/Order services, Buf, sqlc, chi.
- **gRPC microservices:** customer/order/notification with deadlines, interceptors, retries.

## ✅ Level 4 Mastery Checklist

Move on when you can:

- [ ] Design versioned protobuf APIs and enforce them with Buf in CI.
- [ ] Pick REST vs gRPC vs ConnectRPC and defend the choice.
- [ ] Make a consumer idempotent and explain at-least-once delivery.
- [ ] Add tracing/metrics/structured logs to a service and read them in Grafana.
- [ ] Pass a basic security review of your own service.

---

# Level 5 — Mastery (Expert)

**Goal:** Understand *why* Go behaves as it does, optimize with evidence, and
operate distributed systems. This level never really ends.

## 5.1 Runtime & Memory Model

- The Go memory model: happens-before, what synchronization actually guarantees
- Goroutine scheduler internals (G/M/P, work-stealing, preemption)
- Stack growth, escape analysis, heap vs stack allocation

```bash
go build -gcflags='-m' ./...   # see escape-analysis decisions
```

- Garbage collector: tri-color mark-sweep, write barriers, `GOGC`, `GOMEMLIMIT`
- `runtime` package, `GODEBUG` (e.g. `gctrace=1`, `schedtrace`)

## 5.2 Performance Engineering

- Benchmarks: `testing.B`, `b.ReportAllocs()`, `benchstat`
- Profiling: `pprof` (CPU, heap, goroutine, mutex, block)
- Execution tracer: `go test -trace`, `go tool trace`
- Allocation reduction, `sync.Pool`, buffer reuse, avoiding interface boxing
- Optimize **only** with profiles — never by guessing

```bash
go test -bench=. -benchmem -cpuprofile=cpu.out
go tool pprof -http=:0 cpu.out
```

## 5.3 Advanced Concurrency

- Lock-free patterns, `atomic.Pointer`, memory ordering caveats
- `errgroup`, `singleflight`, semaphores, rate limiters
- Detecting and fixing goroutine leaks; deadlock analysis
- Designing cancellation that actually propagates everywhere

## 5.4 Deep Language & Toolchain

- `reflect`: when it's justified, and its cost
- `unsafe`, `//go:linkname`, alignment, `unsafe.Pointer` rules
- `cgo`: costs, build implications, when to avoid it
- Build system: build tags, `//go:embed`, `//go:generate`, linkname, ldflags
- The compiler & linker pipeline at a high level; inlining; PGO (profile-guided optimization)
- `go vet`, `staticcheck`, `golangci-lint`, custom analyzers (`go/analysis`)
- Fuzzing (`go test -fuzz`)

## 5.5 Distributed Systems & Microservices

Master single-service skills first — *then* distribute.

- Service boundaries, API gateway, service discovery
- Distributed transactions, Saga, idempotency, retries with jitter/backoff
- Circuit breakers, bulkheads, timeouts, load balancing
- Each service owns its database; communicate via APIs/events, never shared tables
- Consistency models, clocks, partial failure, the fallacies of distributed computing

```text
api-gateway · auth · customer · order · payment · notification · workflow
sync: REST/gRPC/ConnectRPC   async: Kafka/NATS/PubSub   durable: Temporal
```

## 5.6 Production Operations / SRE

- SLIs/SLOs/error budgets, RED & USE methods
- Capacity planning, load testing, chaos testing
- Kubernetes in depth: probes, resource limits, HPA, rolling updates, Helm,
  network policies, cert-manager, service mesh (Istio/Linkerd)
- Incident response, postmortems, on-call discipline

## 5.7 Beyond the Code

- Read Go source: `runtime`, `sync`, `net/http`, `context`
- Follow Go release notes and proposals; understand the proposal process
- Contribute to open source; review others' Go
- Mentor; write design docs; make and defend trade-offs in public

## Practice — Capstone: Microservices Platform

Build and operate a full platform: api-gateway, auth, customer, order, payment,
notification, and workflow services using Go, Protobuf/Buf, ConnectRPC/gRPC,
Temporal, PostgreSQL, Redis, Kafka, Kubernetes, Prometheus, Grafana, and
OpenTelemetry — with CI/CD, load tests, dashboards, and a written runbook.

## ✅ Expert Mastery Checklist

You've reached Expert when you can:

- [ ] Explain a latency regression from a `pprof`/trace profile, then fix it.
- [ ] Reason about the Go memory model to prove a concurrent algorithm correct.
- [ ] Predict escape-analysis and GC behavior, and tune `GOGC`/`GOMEMLIMIT` deliberately.
- [ ] Design a distributed system that degrades gracefully under partial failure.
- [ ] Operate that system in production with SLOs and own an incident end-to-end.
- [ ] Read unfamiliar standard-library code and explain its design choices.

---

# Recommended Resources

```text
Tour of Go              — first hours
Effective Go            — idioms (read at Level 2)
Go Proverbs             — philosophy
The Go Programming Lang. — the book (Donovan & Kernighan)
Go by Example           — quick reference
Go Blog                 — release notes, deep dives
Go memory model spec    — required for Level 5
Standard library source — your best teacher
```

# Tooling Reference

```bash
go run / build / test / fmt / vet / doc / mod
go test ./... -race -cover
go test -bench=. -benchmem
go build -gcflags='-m'        # escape analysis
go tool pprof / trace          # profiling
govulncheck ./...              # vulnerability scan
golangci-lint run              # aggregate linters
buf lint / breaking / generate # protobuf
```

# Recommended Study Plan (6 Months, then ongoing)

```text
Month 1   Level 1 + start Level 2     → CLI tools, first library
Month 2   Finish Level 2 + Level 3.1  → concurrency, tested library
Month 3   Level 3 (HTTP, DB, auth)    → Task API + Auth Service
Month 4   Level 4 (Proto/RPC, Redis)  → ConnectRPC backend
Month 5   Level 4 (messaging/Temporal)→ workflow-based backend
Month 6   Level 5 (perf, deploy, k8s) → microservices platform
Ongoing   Level 5 mastery             → profiling, internals, OSS
```

# Final Target

After this roadmap you can design, build, secure, observe, deploy, and
**operate** production Go systems: REST/ConnectRPC/gRPC APIs, protobuf
contracts, PostgreSQL repositories, Redis caching, background workers, Temporal
workflows, containerized and Kubernetes-ready services, full observability,
secure auth — and you can reason about the runtime well enough to make them fast.
