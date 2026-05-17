# 🚀 The Professional Go Roadmap — Beginner to Expert

A step-by-step path to becoming a **production-grade Go engineer** 🛠️ — not just someone who knows Go syntax.

This roadmap is organized into **5 levels**. 🪜 Each level has a clear goal 🎯, the
topics to learn 📚, practice projects 🧪, and a **mastery checklist** ✅ that gates
progression. Do not skip levels — each one assumes the previous is solid. 🧱

> 💡 **Mindset:** Becoming good at Go is less about memorizing features and more
> about taste — knowing the *simple, boring, correct* way to do something and
> doing that every time. This roadmap optimizes for taste, not trivia.

---

## 🧭 How to Use This Roadmap

- ⬇️ **Work top to bottom.** Levels build on each other — Level 4 makes no sense without Level 2 reflexes.
  - Don't jump ahead because a topic looks exciting; the prerequisites are load-bearing.
  - If a later topic keeps confusing you, the gap is almost always in an earlier level.
- 🔨 **Build the project(s)** for every level before moving on. Reading ≠ knowing.
  - Type the code yourself; do not copy-paste.
  - Break it on purpose, then fix it — that's where understanding forms.
- 🚧 **Treat each checklist as a gate, not a suggestion.** Be honest with yourself.
  - "I could probably do that" is a fail. "I did that, unaided" is a pass.
- 📖 **Read code, not just tutorials.** The Go **standard library** is the best Go textbook ever written.
  - Pick one stdlib file per week and read it end to end.
- ✍️ **Write Go every day.** Reading about Go does not make you good at Go — fingers on keys does.
  - Even 20 focused minutes beats a weekend binge once a month.
- 🔁 **Expect to revisit earlier levels.** Mastery is a spiral, not a ladder.

### 🗺️ The 5 Levels at a Glance

| 🏷️ Level | Name | 🎯 Focus | ⏱️ Typical Time* |
|-------|------|-------|---------------|
| 1️⃣ | 🌱 Foundations | Syntax, types, tooling, first programs | 2–4 weeks |
| 2️⃣ | 🎨 Idiomatic Go | Interfaces, errors, generics, stdlib, testing | 4–6 weeks |
| 3️⃣ | ⚙️ Concurrency & Services | Goroutines, HTTP, databases, architecture | 6–10 weeks |
| 4️⃣ | 🏭 Production Systems | RPC, messaging, observability, security, deployment | 8–12 weeks |
| 5️⃣ | 🧠 Mastery | Runtime, performance, internals, distributed systems | ♾️ Ongoing |

\* ⚠️ *Assumes consistent daily practice. **Move on by competence, not by calendar.** Some people take twice as long on Level 3 — that's normal.*

### 🎯 Target Stack (where this roadmap leads)

```text
🐹 Language:      Go
🌐 HTTP Router:   net/http + chi
🐘 Database:      PostgreSQL + pgx + sqlc
🔧 Migrations:    goose or golang-migrate
⚡ Cache/Queue:   Redis
📨 Messaging:     NATS / Kafka / Google Pub/Sub
🔄 Workflows:     Temporal
🔌 APIs:          REST + Protocol Buffers + Buf + ConnectRPC + gRPC
🔐 Auth:          JWT + refresh-token rotation
📝 Logging:       slog
🧪 Testing:       testing + testify + testcontainers-go
🚢 Deployment:    Docker + Kubernetes / Cloud Run
📊 Observability: Prometheus + Grafana + OpenTelemetry
```

> 🧩 You will not learn all of this at once. Each tool appears at the level where
> it makes sense — don't reach for Kafka before you can ship a clean HTTP handler.

---

# 1️⃣ Level 1 — 🌱 Foundations (Beginner)

> 🎯 **Goal:** Write, build, and run **correct** Go programs. Be fluent with the toolchain so it never gets in your way.

😀 *By the end of this level you should feel comfortable, not fluent — comfort comes first.*

## 🛠️ 1.1 Setup & Tooling

- 📥 **Install Go** — always the **latest stable release**
  - Go's backward-compatibility promise means newer is safe; never start on an old version
  - Verify with `go version`; know how to manage multiple versions (`go install golang.org/dl/...`)
  - Understand the release cadence (two minor releases/year) and what a patch release fixes
- 📂 **Environment & module cache**
  - `GOROOT` (where Go itself lives) vs `GOPATH` (legacy workspace) — know the difference and why it rarely matters now
  - The module cache at `$GOPATH/pkg/mod` and how `GONOSUMCHECK`/`GOFLAGS`/`GOPROXY` affect downloads
  - `GOBIN` and putting `go install`-ed tools on your `PATH`
- 🧰 **Editor setup** — VS Code + `gopls`, or GoLand
  - Autocomplete, jump-to-definition, find-references, inline diagnostics
  - **Format-on-save** wired up *before* writing real code
  - Run-test and debug-test from the gutter; integrated debugger (`dlv`) working
- ⌨️ **The `go` command** — your daily driver:

```bash
go mod init example.com/myapp   # 🆕 start a module
go run .                        # ▶️ compile + run
go build ./...                  # 🏗️ build everything
go test ./...                   # 🧪 test everything
go fmt ./...                    # 🎨 format (non-negotiable, no debates)
go vet ./...                    # 🔍 static checks for likely bugs
go doc fmt.Println              # 📖 read docs from the terminal
go env                          # ⚙️ inspect your Go environment
go clean -modcache              # 🧹 nuke the module cache when things get weird
```

> 💡 **Tip:** Make `go fmt` automatic on save. Formatting is *not* a personal
> style choice in Go — there is one true format and the tooling enforces it. 🤝

## 📘 1.2 Language Basics

The core of the language. Go is deliberately small — learn all of it.

- 🔢 **Variables & constants**
  - `var`, short declaration `:=`, block declarations, shadowing pitfalls
  - Typed vs untyped constants; constant expressions evaluated at compile time
  - `iota` — counters, skipping values, bit-flag patterns (`1 << iota`)
- 🧮 **Types & zero values**
  - Numeric types, overflow/wrap behavior, `int` vs `int64` portability
  - **Every type has a zero value** — `0`, `""`, `false`, `nil` — internalize this
  - Explicit conversions only (no implicit numeric promotion); `byte`/`rune` aliases
- 🔀 **Control flow**
  - `if` with init statement; no parentheses, mandatory braces
  - `for` is the *only* loop — three-clause, while-style, infinite, `range`
  - `switch` — expression vs tagless, no implicit fallthrough, `fallthrough`, type switch preview
  - Labels with `break`/`continue`; `goto` (and why you almost never use it)
- 🧩 **Functions**
  - Multiple return values; the `(result, err)` convention
  - Named returns — when they clarify, when they hide bugs
  - Variadics (`...T`), passing a slice as variadic
  - First-class functions, closures, capturing loop variables correctly
- ⏮️ **`defer`**
  - LIFO execution order
  - Arguments evaluated at `defer` time, not at execution time
  - Deferring in loops (resource leaks), `defer` + named returns interaction
- 📦 **Arrays vs slices**
  - Arrays are values (copied); slices are headers (ptr/len/cap)
  - `append` growth, capacity doubling, reslicing, `s[low:high:max]` full-slice expression
  - **Aliasing bugs** — sharing backing arrays, `copy`, the #1 beginner trap
- 🗺️ **Maps**
  - Zero value is `nil`; reading is fine, **writing panics**
  - Comma-ok idiom (`v, ok := m[k]`), `delete`, length
  - Randomized iteration order (intentional); maps are not safe for concurrent writes
- 🔤 **Strings, runes, bytes**
  - Strings are immutable UTF-8 byte sequences; `len` is bytes, not characters
  - `range` over a string yields runes + byte offsets
  - `[]byte` ↔ `string` conversions (and their copy cost), `strings`/`unicode/utf8`
- 🏗️ **Structs**
  - Field ordering, tags, comparability, anonymous structs
  - **Embedding** — method/field promotion, name collisions, embedding interfaces
  - Value vs pointer receivers (preview of methods)
- 👉 **Pointers**
  - `&` / `*`, nil pointer dereference, when to take a pointer
  - *Why Go deliberately has no pointer arithmetic* (safety, GC)
- 📂 **Packages & modules**
  - Exported vs unexported by capitalization
  - `go.mod` (requires, go directive), `go.sum`, semantic import versioning
  - `import` paths, package name vs path, `internal/` visibility rule

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, Go 👋")
}
```

> ⚠️ **Aliasing & nil-map bugs cause more beginner pain than anything else.**
> Deliberately reproduce both until you can predict them in your sleep. 😴

## ⚠️ 1.3 Errors From Day One

Go's error handling is **explicit and value-based** — there are no exceptions. Learn it correctly *now*; bad habits are very hard to unlearn later. 🔒

```go
// ❌ Bad — silently swallows failures. This is how production incidents are born.
result, _ := doSomething()

// ✅ Good — handle the error or propagate it. Always.
result, err := doSomething()
if err != nil {
    return err
}
```

- 🚫 **`if err != nil` is not boilerplate to resent**
  - It's the language being honest about what can fail
  - Every ignored error is a future incident waiting for a bad input
- 🧵 **Errors are just values**
  - You can return them, store them, compare them, and wrap them
  - The `error` interface is one method: `Error() string` (full depth in Level 2)
- 🙅 **Discarding errors**
  - Never use `_` on an error unless you can *explain out loud* why it's safe
  - Common safe cases (and their reasoning): `fmt.Fprintf` to a buffer, `defer f.Close()` on a read-only file — know *why*

## 🧪 Practice

Build all three. Small, but finish them. 🏁

- 🔁 **String reverser + word counter CLI** — read stdin/args, handle Unicode correctly.
- 🎲 **Number-guessing game** — random target, higher/lower hints, attempt count, replay loop.
- 📄 **Tiny `wc`** — read a file, print line / word / byte counts, handle missing-file errors.

## ✅ Level 1 Mastery Checklist

🚪 Move on when you can:

- [ ] 🍰 Explain the difference between a slice and an array, and **predict an aliasing bug** before running the code.
- [ ] 🏗️ Build and run a **multi-file module** without help or copy-paste.
- [ ] 💥 Explain *why* `result, _ :=` is dangerous, with a real failure scenario.
- [ ] 📖 Use `go doc` instead of reaching for a browser.
- [ ] 🧼 Write a 100-line program with **zero `go vet` warnings** and clean `gofmt`.

---

# 2️⃣ Level 2 — 🎨 Idiomatic Go (Advanced Beginner)

> 🎯 **Goal:** Write Go that a **senior Go engineer would approve in code review** — clear, small, and conventional.

🙂 *This is the level that separates "I can write Go" from "I write good Go."*

## 🧩 2.1 Interfaces & Composition

- 🤝 **Implicit satisfaction**
  - No `implements` keyword; a type satisfies an interface by having the methods
  - *"Accept interfaces, return structs"* — and why this keeps APIs flexible
- 🤏 **Small interfaces**
  - `io.Reader`, `io.Writer`, `error`, `fmt.Stringer` — one or two methods
  - Interface segregation; composing big interfaces from small ones (`io.ReadWriter`)
- 🕳️ **Interface values**
  - The `(type, value)` pair model
  - The **`nil`-interface pitfall**: a non-nil interface holding a nil concrete pointer
  - Comparing interfaces, panics from comparing uncomparable dynamic types
- 🔎 **Type assertions & switches**
  - `v, ok := x.(T)` safe form vs panicking form
  - Type switch on dynamic type; handling the `default` case
- 🧬 **Composition over inheritance**
  - Struct embedding for code reuse; interface embedding for contract reuse
  - Decorator/middleware patterns built from embedding
- 📐 **Where to define interfaces**
  - Define them **where they're consumed**, not where implemented
  - Avoid speculative interfaces with a single implementation

## ⚠️ 2.2 Errors, Properly

- 🏁 **Sentinel errors**
  - `var ErrNotFound = errors.New("...")`, compared with `errors.Is`
  - When sentinels are appropriate vs when they over-couple callers
- 🧱 **Custom error types**
  - Structs implementing `error`, carrying fields (codes, IDs)
  - Extracting with `errors.As`; pointer vs value receiver on `Error()`
- 🎁 **Wrapping**
  - `fmt.Errorf("...: %w", err)` adds context, preserves the chain
  - `%w` vs `%v` (wrap vs flatten); multiple `%w` (Go 1.20+ `errors.Join`)
- 💣 **`panic` / `recover`**
  - Only for truly exceptional, unrecoverable situations — not control flow
  - `recover` only works in a deferred function; recovering at goroutine boundaries
- 📜 **Error contract design**
  - Document what errors a function returns and how to test for them
  - Decide per-package: sentinel, typed, or opaque-with-wrap

```go
var ErrNotFound = errors.New("not found")

func GetUser(id string) (*User, error) {
    u, err := db.Find(id)
    if err != nil {
        return nil, fmt.Errorf("get user %s: %w", id, err) // 🎁 wrap with context
    }
    return u, nil
}

// 🔎 Caller can still detect the root cause through the wrap:
if errors.Is(err, ErrNotFound) { /* ... */ }
```

> 💡 Add context as the error travels *up* the stack. Each layer answers
> "what was I doing when this failed?" 🧵

## 🧬 2.3 Generics

- 🔧 **Type parameters & constraints**
  - `func F[T any](...)`, type sets, the `constraints` package
  - `any` vs `comparable`; defining custom constraint interfaces with unions (`~int | ~string`)
- ✅ **When generics help**
  - Generic containers (`Set[T]`, `Stack[T]`), algorithms (`Map`, `Filter`, `Reduce`)
  - Eliminating `interface{}` + type-assertion boilerplate
- ❌ **When generics hurt**
  - Premature abstraction, one-off use, harming readability
  - When an interface or plain function is simpler
- 🆚 **Functions vs types & inference**
  - Generic functions vs generic types and methods
  - Type inference rules and where you must specify type arguments explicitly

> 🧘 **Maturity signal:** choosing *not* to use generics when an interface or
> plain function is clearer. Reach for the simplest tool that works.

## 📚 2.4 Standard Library Fluency

The stdlib is huge and excellent. Get comfortable in the daily-driver packages:

- 🔤 **Text & I/O building blocks**
  - `fmt` (verbs, `Stringer`, `%+v`/`%#v`), `strings.Builder` vs concatenation
  - `strconv` parsing/formatting, `bytes.Buffer`, `bufio.Scanner` for line input
- 📦 **`encoding/json`**
  - Marshal/unmarshal, struct tags, `omitempty`, `-`, embedded structs
  - Custom `MarshalJSON`/`UnmarshalJSON`, `json.RawMessage`, streaming with `json.Decoder`
  - Pitfalls: unexported fields, numbers as `float64`, unknown fields
- ⏰ **`time`**
  - `Duration` arithmetic, the **reference-time** layout string (`2006-01-02`)
  - Monotonic vs wall clock, `time.Timer`/`Ticker` and stopping them, time zones & `Location`
- 🗂️ **`io` / `os`**
  - The reader/writer mindset, `io.Copy`, `io.EOF`, `io.MultiWriter`, `io.Pipe`
  - Files, permissions, `os.Args`, `os.Getenv`, `os.Exit` (and why `defer` won't run)
- 🧮 **`sort`, `slices`, `maps`**
  - Generic `slices.Sort`, `slices.Contains`, `slices.Index`, `maps.Keys`
  - Custom ordering with `slices.SortFunc`; replacing hand-written loops

> 📖 **Read the source** of these packages. They are written by experts to be
> read by you — copy their style.

## 🧪 2.5 Testing & Project Layout

- 🧪 **The `testing` package**
  - Table-driven tests, subtests with `t.Run`, parallel tests with `t.Parallel()`
  - `t.Cleanup`, `t.Setenv`, `t.TempDir`
- 🛠️ **Fixtures & helpers**
  - `t.Helper()` for clean failure lines, **golden files**, the `testdata/` convention
  - Deterministic tests: inject clocks/randomness, avoid sleeps
- 🎛️ **Running tests**
  - `-run` regex, `-v`, `-cover`/`-coverprofile`, `-count=1` to defeat the cache
  - `go test -race` as a habit, not an afterthought
- 📈 **Benchmarks**
  - `testing.B`, `b.ResetTimer`, `b.ReportAllocs`, avoiding compiler elimination
- 🏛️ **Project layout**
  - `cmd/` (entrypoints), `internal/` (private), `pkg/` (intentionally public)
  - *Not* over-structuring early; let structure emerge from need
- 🧠 **Philosophy**
  - The Go Proverbs, *Effective Go*, "clear is better than clever"

```go
func TestAdd(t *testing.T) {
    cases := []struct {
        name       string
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

> ✅ Table-driven tests should become a **reflex**, not a technique you recall.

## 🧪 Practice

- 🧾 **JSON-driven CLI** — parse config, validate it, pretty-print results, exit codes.
- 📦 **Generic `Set[T]` and `Stack[T]`** — full edge-case coverage, benchmarks.
- 📚 **Small library** — clean public API, godoc comments, runnable examples, **≥80% coverage**.

## ✅ Level 2 Mastery Checklist

🚪 Move on when you can:

- [ ] 🕳️ Explain the `nil`-interface gotcha and **produce it on demand**.
- [ ] ⚖️ Choose between sentinel errors, error types, and wrapping — and **justify** the choice.
- [ ] 🧪 Write table-driven tests with subtests **by reflex**.
- [ ] 📖 Read and explain a **non-trivial file** from the Go standard library.
- [ ] 🧘 Decide **not** to use generics when they add no value.

---

# 3️⃣ Level 3 — ⚙️ Concurrency & Services (Intermediate)

> 🎯 **Goal:** Build **correct concurrent programs** and ship a **real HTTP service** backed by a database.

😅 *This level is where most people struggle and where the most growth happens. Be patient with the race detector.*

## 🔀 3.1 Concurrency

- 🧵 **Goroutines & the scheduler**
  - `go` statement cost, M:N model, `GOMAXPROCS`, cooperative + async preemption
  - Why goroutines are cheap but not free; goroutine lifetime ownership
- 📡 **Channels**
  - Unbuffered (rendezvous) vs buffered (queue) semantics
  - Directionality (`chan<-`, `<-chan`), closing rules, `range` over channels, nil-channel behavior
- 🎚️ **`select`**
  - Multiplexing, timeouts with `time.After`, `default` for non-blocking, the empty `select{}`
  - Avoiding leaks from a blocked send in `select`
- 🔒 **`sync`**
  - `WaitGroup` (Add before go), `Mutex`/`RWMutex`, `Once`, `Pool`
  - Lock granularity, defer-unlock pattern, copying a struct containing a mutex (don't)
- ⚛️ **`sync/atomic`**
  - `atomic.Int64`/`Bool`/`Pointer`; when a plain mutex is *clearer* than clever atomics
- 🧭 **`context.Context`**
  - `WithCancel`/`WithTimeout`/`WithDeadline`/`WithValue`
  - Cancellation **propagation through every layer**; never store context in a struct; values for request-scoped data only
- 🏭 **Concurrency patterns**
  - Worker pool, fan-in/fan-out, pipeline, bounded concurrency (semaphore), or-done
- 🛑 **Graceful shutdown**
  - Signal handling, draining in-flight work, deadline on shutdown
- 🏁 **The race detector**
  - `go test -race`, `go run -race`; understanding a race report; run it constantly

```go
ctx, cancel := context.WithTimeout(context.Background(), 3*time.Second)
defer cancel()

results := make(chan int)
var wg sync.WaitGroup
for _, job := range jobs {
    wg.Add(1)
    go func(j Job) {            // 🧵 each worker is a goroutine
        defer wg.Done()
        select {
        case results <- process(j):
        case <-ctx.Done():       // 🧭 respect cancellation/timeout
        }
    }(job)
}
go func() { wg.Wait(); close(results) }() // 🚪 close when all workers finish
```

> 🧭 **`context.Context` shows up everywhere** — HTTP handlers, DB queries,
> gRPC/ConnectRPC, background jobs, Temporal activities, external calls.
> Understand it *deeply*; it's the backbone of cancellation in real systems. 🦴

## 🌐 3.2 HTTP Servers

🏗️ Learn the **standard library first**, *before* any framework. `net/http` is excellent.

```go
func main() {
    mux := http.NewServeMux()
    mux.HandleFunc("GET /healthz", func(w http.ResponseWriter, r *http.Request) {
        w.Write([]byte("ok ✅"))
    })

    srv := &http.Server{Addr: ":8080", Handler: mux}
    log.Fatal(srv.ListenAndServe())
}
```

- 🧭 **Routing & handlers**
  - `http.Handler` vs `HandlerFunc`, Go 1.22 method+path patterns, path values
  - Request lifecycle, `r.Context()`, request body limits
- 🔗 **Middleware**
  - The `func(http.Handler) http.Handler` pattern, chaining order, per-route vs global
- 📦 **Request/response handling**
  - JSON decode with `DisallowUnknownFields`, validation, consistent error responses
  - Setting status codes correctly, streaming responses, `http.Error`
- 🛡️ **Cross-cutting concerns**
  - CORS, request IDs, structured logging, auth, panic-recovery middleware
- 🛑 **Server lifecycle**
  - Timeouts (`ReadHeaderTimeout`, `IdleTimeout`), `http.Server.Shutdown`, graceful drain
- 🧰 **Router choice**
  - **`net/http` + `chi`** (idiomatic, stdlib-compatible); alternatives: Gin, Echo, Fiber

## 🔌 3.3 REST API Design

```http
GET    /users        GET    /users/{id}
POST   /users        PATCH  /users/{id}
PUT    /users/{id}   DELETE /users/{id}
```

- ✅ **Methods & status codes**
  - Correct verb semantics, 2xx/4xx/5xx meaning, `201` + `Location`, `204` for no body
- 📄 **Collections**
  - Pagination (offset vs cursor), filtering, sorting, sparse fieldsets
  - API **versioning** strategy (URL vs header) and deprecation policy
- 🔁 **Reliability semantics**
  - Idempotency keys for unsafe retries, rate limiting, conditional requests (ETag)
- 📦 **Error envelope**
  - Stable machine-readable `code` + human `message`; *never leak internals or stack traces*
- 📜 **Documentation**
  - OpenAPI/Swagger, generated clients, contract as source of truth

```json
{ "code": "USER_NOT_FOUND", "message": "User not found" }
```

## 🐘 3.4 Databases (PostgreSQL)

- 🗄️ **SQL fundamentals**
  - Primary/foreign keys, constraints, normalization basics
  - **Indexes** (B-tree, composite, partial), what makes a query use one
  - Joins, `NULL` semantics, aggregation, common gotchas
- 🔁 **Transactions**
  - ACID, isolation levels, anomalies (dirty/non-repeatable/phantom), `SELECT ... FOR UPDATE`
  - Transaction scope in Go, `defer tx.Rollback()` pattern
- 🔬 **Performance**
  - `EXPLAIN ANALYZE`, reading a plan, N+1 queries, connection pool sizing
- 🏭 **Go data layer**
  - **PostgreSQL + `pgx` + `sqlc`** (beginner-friendly alternative: GORM)
  - `database/sql` semantics, context-aware queries, scanning into structs
- 🔧 **Migrations**
  - `goose`, `golang-migrate`, or `atlas`; forward-only discipline, reversible vs not
  - Migrations are code — reviewed, versioned, tested against a real DB

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

> 💡 `sqlc` generates type-safe Go from SQL — you write SQL, it writes the
> boilerplate. Fewer runtime surprises. 🎯

## 🏛️ 3.5 Clean Architecture

```text
🌐 Handler / Controller  →  🧠 Service / Usecase  →  🗄️ Repository  →  🐘 Database
```

- 🚫 **Thin handlers**
  - Handlers translate transport ↔ domain; no business rules in handlers
  - Map domain errors to HTTP status in one place
- 🗄️ **Layer responsibilities**
  - Repositories own data access; services own business rules; entities own invariants
- 🧩 **Interfaces for seams**
  - Use interfaces where they aid testing or decoupling — not everywhere
  - Dependency injection via constructors, not globals
- 🛑 **Avoid premature abstraction**
  - Duplication is cheaper than the wrong abstraction
  - Refactor toward patterns when the third use case appears, not the first

## 🔐 3.6 Authentication & Authorization

- 🔑 **Password handling**
  - `bcrypt`/`argon2` with sane cost params; **never store raw passwords**
  - Constant-time comparison; password reset flows that don't leak account existence
- 🎟️ **Tokens**
  - JWT access tokens (short-lived) + refresh tokens (long-lived)
  - **Refresh-token rotation**, reuse detection, revocation lists/families
  - Store only token **hashes**; **never log** tokens or passwords
- 👮 **Authorization models**
  - RBAC vs permission/attribute-based, API keys, OAuth2/OIDC basics, scopes
- ⏱️ **Abuse resistance**
  - Rate-limit login & OTP endpoints, account lockout/backoff, audit auth events

> 🛡️ Auth is where small mistakes become breaches. Slow down here. 🐢

## 🧪 3.7 Testing Strategy

- 🧱 **Test layers**
  - Unit (services, pure logic), integration (repositories via `testcontainers-go`), API (`httptest`)
  - The test pyramid: many fast unit tests, fewer integration, fewest e2e
- 🎭 **Test doubles**
  - Mocks/fakes/stubs, `gomock`/`mockery`, mock at boundaries not internals
- 🎯 **Prioritization**
  - Test the layers that hold business value first; assert behavior, not implementation

## 🐳 3.8 Docker

```dockerfile
FROM golang:1-alpine AS build
WORKDIR /src
COPY go.mod go.sum ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 go build -o /bin/server ./cmd/api

FROM gcr.io/distroless/static          # 🪶 tiny, no shell, smaller attack surface
COPY --from=build /bin/server /server
EXPOSE 8080
ENTRYPOINT ["/server"]
```

- 🧱 **Image construction**
  - Multi-stage builds, layer caching order (`go.mod` before source), `.dockerignore`
  - Minimal/distroless base, non-root user, static binary (`CGO_ENABLED=0`)
- 🧩 **Local environment**
  - Docker Compose for Postgres/Redis, healthchecks, deterministic seed data

## 🧪 Practice — Project: Task API → Auth Service

1. 📋 **Task API:** chi + PostgreSQL + Docker. CRUD, pagination, graceful shutdown.
2. 🔐 **Auth Service:** register / login / refresh / logout, RBAC, token rotation, Redis, integration tests.

## ✅ Level 3 Mastery Checklist

🚪 Move on when you can:

- [ ] 🏁 Build a concurrent program that **passes `-race`** and **shuts down cleanly**.
- [ ] ⚖️ Explain **when to use a channel vs a mutex vs an atomic**.
- [ ] 🚢 Ship a **chi + PostgreSQL** service with migrations and integration tests.
- [ ] 🔄 Implement **refresh-token rotation** correctly.
- [ ] 🏛️ **Justify your architecture layering** in a code review.

---

# 4️⃣ Level 4 — 🏭 Production Systems (Advanced)

> 🎯 **Goal:** Build, secure, observe, and deploy systems that **survive real traffic** 🌊 and real failure.

😎 *This is where you start building things other engineers depend on.*

## 📜 4.1 Protocol Buffers, Buf, ConnectRPC & gRPC

🏆 Recommended stack: **Protocol Buffers + Buf + ConnectRPC + gRPC**.

### 📐 Protobuf — strongly-typed API contracts

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

- 🔢 **Wire compatibility rules**
  - Never reuse/rename field numbers; mark removed fields/numbers `reserved`
  - Field presence, default values, why proto3 scalars aren't nullable (use wrappers/`optional`)
- 🧬 **Schema design**
  - Version packages (`user.v1`), explicit request/response messages, enums with a zero `UNSPECIFIED`
  - Pagination/list conventions, well-known types (`Timestamp`, `FieldMask`)

### 🧰 Buf — manage protos (codegen, lint, breaking-change detection, CI)

```yaml
# buf.yaml
version: v2
modules: [{ path: proto }]
lint: { use: [STANDARD] }
breaking: { use: [FILE] }
```

```bash
buf lint && buf breaking && buf generate   # 🧹 lint  🚨 breaking  ⚙️ codegen
```

- 🧹 **Lint** — enforce naming/style standards across all protos
- 🚨 **Breaking-change detection** — gate PRs in CI against the main branch
- ⚙️ **Codegen** — `buf generate` with pinned plugin versions; the BSR for sharing modules

### 🌉 ConnectRPC — protobuf APIs over HTTP/1.1, HTTP/2, gRPC & gRPC-Web

Browser-friendly, mounts directly on `net/http`/chi — no Envoy proxy needed:

```go
mux := http.NewServeMux()
path, handler := userv1connect.NewUserServiceHandler(&UserServer{})
mux.Handle(path, handler)
http.ListenAndServe(":8080", mux)
```

- 🌐 Speaks gRPC, gRPC-Web, and its own simple HTTP/JSON protocol
- 🧩 Interceptors (the middleware equivalent), client + handler from the same schema
- 🧪 Curl-able JSON for debugging without special tooling

### ⚡ gRPC — high-performance service-to-service RPC

- 🔁 **RPC styles** — unary, server/client/bidi streaming and when each fits
- ⏱️ **Call mechanics** — deadlines/cancellation, metadata, status codes
- 🔗 **Cross-cutting** — interceptors, TLS/mTLS, reflection, health checks, keepalive

### 🤔 Choosing the right protocol

```text
🌍 REST        → public HTTP APIs, simple CRUD, third-party integrations
⚡ gRPC        → internal service-to-service, high throughput
🌉 ConnectRPC  → browser/mobile + protobuf contracts without an Envoy proxy
```

🎯 Map errors to **proper status codes**:

```go
// ⚡ gRPC
return nil, status.Error(codes.NotFound, "user not found")
// 🌉 Connect
return nil, connect.NewError(connect.CodeNotFound, errors.New("user not found"))
```

## ⚡ 4.2 Redis, Caching & Background Jobs

- 🧰 **Use cases**
  - Cache, rate limiting, sessions, distributed locks, queues, OTP store, idempotency keys
  - Data structures: strings, hashes, sets, sorted sets, streams — pick the right one
- ⏳ **Correctness primitives**
  - TTL/expiry, atomic ops (`INCR`, `SETNX`), Lua scripts for multi-step atomicity
  - Distributed locks done safely (token + expiry), the limits of single-node locks
- 🔁 **Caching patterns**
  - Cache-aside (lazy), write-through, invalidation strategy, negative caching
  - **Stampede protection**: jittered TTLs, single-flight, request coalescing
- 🧵 **Background jobs**
  - Persist → enqueue → worker → update; visibility timeout; retry with backoff; idempotent handlers

> ⚠️ The two hard problems: cache **invalidation** and stampedes. Design for
> them up front, not after the incident. 🔥

## 📨 4.3 Messaging & Event-Driven Systems

- 🚌 **Brokers & trade-offs**
  - Redis Streams, NATS/JetStream, RabbitMQ, Kafka, Google Pub/Sub — throughput vs ordering vs retention
- 🔁 **Delivery semantics**
  - At-least-once vs at-most-once vs effectively-once; **consumer idempotency** is mandatory
  - Retries, backoff, **dead-letter queues**, poison messages, partition/ordering keys
- 🧬 **Event design**
  - Event vs command, schema **versioning** & evolution, envelope metadata, contract ownership
- 🧱 **Reliability patterns**
  - **Outbox** (atomic DB write + publish), **Saga** (choreography vs orchestration), idempotency store

```json
{ "event_id": "evt_123", "event_type": "ORDER_CREATED",
  "occurred_at": "2026-01-01T10:00:00Z", "data": { "order_id": "ord_123" } }
```

> 🧠 **Assume every message can arrive twice and out of order.** If your
> consumer is idempotent, you've already won. 🏆

## 🔄 4.4 Temporal — Durable Workflows

Use when processes need **retries, timeouts, human approval, compensation**, or
**long-running durable execution** that must survive process restarts.

```text
🔄 Workflow · 🧱 Activity · 👷 Worker · 📥 Task Queue · 📣 Signal · ❓ Query
⏲️ Timer · 🔁 Retry Policy · 👶 Child Workflow · ♻️ Continue-As-New

📏 Rule: Workflows orchestrate. Activities do work. Services own business logic.
```

- 🧠 **Core model** — workflow vs activity, task queues, workers, event history & replay
- 🧩 **Determinism** — why workflow code must be deterministic; what belongs in an activity
- ⏲️ **Durability features** — timers, retry policies, signals/queries, child workflows, continue-as-new
- 🧯 **Failure handling** — compensation/Saga, heartbeats for long activities, idempotent activities

## 🔧 4.5 Configuration & Secrets

- ⚙️ **Configuration**
  - Env vars → typed config struct, validation **at startup** (fail fast), per-environment overrides
  - 12-factor config; no environment-specific code branches
- 🔐 **Secrets**
  - Vault / cloud secret managers / K8s Secrets; injection at runtime, not bake-time
  - **Never in Git**; rotation strategy; least-privilege access to secrets

> 🚨 A secret committed to Git is a leaked secret forever (even after `git rm`).
> Rotate it, don't just delete it. 🔁

## 📊 4.6 Logging & Observability

- 📝 **Structured logging**
  - `slog` (alternatives: `zap`, `zerolog`), levels, key/value context, no PII
  - One logger, propagated via context; sampling noisy logs
- 📈 **Metrics**
  - Prometheus: counters/gauges/histograms, RED method, cardinality discipline
- 🔍 **Tracing**
  - OpenTelemetry spans, context propagation across services, trace ↔ log correlation
- 🧵 **Operational endpoints**
  - Request/correlation IDs, audit logs, `/healthz` (liveness) vs `/readyz` (readiness)

```go
slog.Info("user created", "user_id", user.ID, "tenant", tenantID) // 🔑 key/value, queryable
```

> 👀 You can't fix what you can't see. Observability is not optional in
> production — it's how you sleep at night. 😴

## 🛡️ 4.7 Security

- 🔒 **Transport & input**
  - HTTPS/TLS, mTLS for service-to-service, strict input validation
  - **Parameterized SQL** only — never string-built queries
- 🛡️ **Web vectors**
  - CORS, CSRF, XSS, clickjacking; secure headers (HSTS, CSP); JWT alg/exp/aud validation
- 🔬 **Supply chain**
  - `govulncheck`, dependency pinning, minimal images, least privilege, SBOM awareness
- 🔐 **Data protection**
  - Encryption at rest and in transit; **never log** passwords, tokens, national IDs, card numbers

## 🚢 4.8 Deployment & CI/CD

- 🪜 **Progression**
  - Start simple: VPS + Docker Compose + Nginx + systemd
  - Graduate to Cloud Run / ECS / Kubernetes when it earns its complexity
- 🤖 **Pipeline stages**
  - fmt/vet → race tests → buf lint/breaking → build → push → migrate → deploy
  - Build once, promote the same artifact across environments; automated rollback

```text
push → 🎨 fmt/vet → 🧪 go test ./... -race → 📜 buf lint/breaking
     → 🐳 docker build → 📤 push → 🔧 migrate → 🚀 deploy
```

## 🧪 Practice — Projects

- 🛒 **E-commerce backend:** products, orders, payments, webhooks, background workers, OpenAPI.
- 🌉 **ConnectRPC backend:** User/Auth/Product/Order services, Buf, sqlc, chi.
- ⚡ **gRPC microservices:** customer/order/notification with deadlines, interceptors, retries.

## ✅ Level 4 Mastery Checklist

🚪 Move on when you can:

- [ ] 📐 Design **versioned protobuf APIs** and enforce them with Buf in CI.
- [ ] 🤔 Pick **REST vs gRPC vs ConnectRPC** and **defend the choice**.
- [ ] 🔁 Make a consumer **idempotent** and explain at-least-once delivery.
- [ ] 📊 Add tracing/metrics/structured logs and **read them in Grafana**.
- [ ] 🛡️ Pass a **basic security review** of your own service.

---

# 5️⃣ Level 5 — 🧠 Mastery (Expert)

> 🎯 **Goal:** Understand **why** Go behaves as it does, optimize with **evidence**, and **operate** distributed systems.

♾️ *This level never really ends — and that's the fun part.* 🤓

## ⚙️ 5.1 Runtime & Memory Model

- 🧠 **The Go memory model**
  - Happens-before, what channels/mutexes/atomics actually guarantee
  - Why data races are undefined behavior, not "just a stale read"
- 🧵 **Scheduler internals**
  - G/M/P model, run queues, work-stealing, sysmon, preemption (loop & async)
  - Blocking syscalls, network poller, `GOMAXPROCS` effects
- 📈 **Allocation & escape analysis**
  - Stack vs heap, what forces an escape, inlining's effect on escapes
- 🗑️ **Garbage collector**
  - Tri-color concurrent mark-sweep, write barriers, GC pacing
  - Tuning `GOGC` and `GOMEMLIMIT` deliberately, soft-memory-limit behavior
- 🔧 **Introspection**
  - `runtime` package, `GODEBUG` (`gctrace=1`, `schedtrace`, `scheddetail`)

```bash
go build -gcflags='-m' ./...   # 🔬 see escape-analysis decisions
```

## 🚀 5.2 Performance Engineering

- 📏 **Benchmarking**
  - `testing.B`, `b.ReportAllocs()`, avoiding dead-code elimination, `benchstat` for significance
- 🔥 **Profiling**
  - `pprof`: CPU, heap, goroutine, mutex, block profiles; `net/http/pprof` in prod
  - Reading flame graphs, top/list/peek, allocation vs in-use heap
- 🕰️ **Execution tracing**
  - `go test -trace`, `go tool trace`: scheduler latency, GC pauses, syscall blocking
- ♻️ **Optimization techniques**
  - Allocation reduction, `sync.Pool`, buffer reuse, avoiding interface boxing, preallocation
- 🎯 **Discipline**
  - Optimize **only with profiles**; measure → change one thing → measure again

```bash
go test -bench=. -benchmem -cpuprofile=cpu.out
go tool pprof -http=:0 cpu.out   # 🔥 interactive flame graphs
```

> 📊 **Measure, change one thing, measure again.** Performance work without a
> profile is just superstition. 🔮

## 🔀 5.3 Advanced Concurrency

- 🔓 **Lock-free & atomics**
  - `atomic.Pointer`, CAS loops, memory-ordering caveats, when *not* to go lock-free
- 🧰 **Coordination libraries**
  - `errgroup` (bounded, cancel-on-error), `singleflight`, `golang.org/x/sync/semaphore`, rate limiters
- 🕳️ **Leak & deadlock analysis**
  - Detecting goroutine leaks (pprof goroutine profile), lock-ordering deadlocks
- 🧭 **Cancellation design**
  - Making cancellation propagate everywhere; context-aware blocking calls only

## 🔬 5.4 Deep Language & Toolchain

- 🪞 **`reflect`**
  - When it's justified (serialization, DI), its cost, `reflect.Value`/`Type` model
- ⚠️ **`unsafe` & low-level**
  - `unsafe.Pointer` rules, alignment, `//go:linkname`, when this is acceptable
- 🔗 **`cgo`**
  - Call overhead, build/cross-compile implications, when to avoid entirely
- 🏗️ **Build system**
  - Build tags, `//go:embed`, `//go:generate`, `-ldflags` for version stamping
- 🧮 **Compiler & PGO**
  - Compile→SSA→link pipeline at a high level, inlining heuristics, profile-guided optimization
- 🧹 **Static analysis**
  - `go vet`, `staticcheck`, `golangci-lint`, writing custom analyzers with `go/analysis`
- 🐝 **Fuzzing**
  - `go test -fuzz`, seed corpus, finding inputs you didn't think of

## 🌐 5.5 Distributed Systems & Microservices

🧱 **Master single-service skills first — *then* distribute.** Distribution multiplies failure modes.

- 🚪 **Topology**
  - Service boundaries (DDD-ish), API gateway, service discovery, north-south vs east-west
- 🔁 **Consistency & transactions**
  - Saga, idempotency, retries with jitter/backoff, exactly-once illusion
- 🔌 **Resilience patterns**
  - Circuit breakers, bulkheads, timeouts everywhere, load balancing, backpressure
- 🗄️ **Data ownership**
  - Each service owns its DB; integrate via APIs/events, **never shared tables**
- ⏱️ **Hard realities**
  - Consistency models, clocks/ordering, partial failure, the fallacies of distributed computing

```text
🚪 api-gateway · 🔐 auth · 👤 customer · 📦 order · 💳 payment · 🔔 notification · 🔄 workflow
🔁 sync: REST/gRPC/ConnectRPC   📨 async: Kafka/NATS/PubSub   ⏳ durable: Temporal
```

## 🚨 5.6 Production Operations / SRE

- 🎯 **Reliability targets**
  - SLIs/SLOs/error budgets, RED (rate/errors/duration) & USE (utilization/saturation/errors)
- 📦 **Capacity & resilience testing**
  - Load testing, soak tests, chaos testing, failure injection
- ☸️ **Kubernetes in depth**
  - Liveness/readiness/startup probes, requests/limits, HPA/VPA, rolling/blue-green/canary
  - Helm, network policies, cert-manager, service mesh (Istio/Linkerd)
- 🧯 **Incident discipline**
  - On-call rotation, runbooks, blameless postmortems, error-budget-driven prioritization

## 🌟 5.7 Beyond the Code

- 📖 **Read Go source** — `runtime`, `sync`, `net/http`, `context`; understand the *why*
- 📰 **Track the language** — release notes, the proposal process, accepted/rejected proposals
- 🤝 **Contribute** — open source, code review, issue triage
- 🧑‍🏫 **Multiply yourself** — mentor, write design docs, defend trade-offs in public

## 🧪 Practice — Capstone: Microservices Platform 🏗️

Build **and operate** a full platform: api-gateway, auth, customer, order,
payment, notification, and workflow services using Go, Protobuf/Buf,
ConnectRPC/gRPC, Temporal, PostgreSQL, Redis, Kafka, Kubernetes, Prometheus,
Grafana, and OpenTelemetry — with CI/CD, load tests, dashboards, and a written
**runbook**. 📒

## ✅ Expert Mastery Checklist

🏔️ You've reached **Expert** when you can:

- [ ] 🔥 Explain a **latency regression** from a `pprof`/trace profile, then fix it.
- [ ] 🧠 Reason about the **Go memory model** to prove a concurrent algorithm correct.
- [ ] 📈 Predict **escape-analysis and GC behavior**, and tune `GOGC`/`GOMEMLIMIT` deliberately.
- [ ] 🛡️ Design a distributed system that **degrades gracefully** under partial failure.
- [ ] 🚨 **Operate** that system in production with SLOs and own an incident end-to-end.
- [ ] 📖 Read **unfamiliar standard-library code** and explain its design choices.

---

# 📚 Recommended Resources

```text
🧭 Tour of Go              — first hours
📘 Effective Go            — idioms (read at Level 2)
🧠 Go Proverbs             — philosophy
📖 The Go Programming Lang. — the book (Donovan & Kernighan)
⚡ Go by Example           — quick reference
📰 Go Blog                 — release notes, deep dives
📜 Go memory model spec    — required for Level 5
🏆 Standard library source — your best teacher
```

# 🧰 Tooling Reference

```bash
go run / build / test / fmt / vet / doc / mod   # 🛠️ daily drivers
go test ./... -race -cover                      # 🏁 correctness + coverage
go test -bench=. -benchmem                      # 📏 performance
go build -gcflags='-m'                          # 🔬 escape analysis
go tool pprof / trace                           # 🔥 profiling
govulncheck ./...                               # 🛡️ vulnerability scan
golangci-lint run                               # 🧹 aggregate linters
buf lint / breaking / generate                  # 📜 protobuf
```

# 🗓️ Recommended Study Plan (6 Months, then ongoing)

```text
📅 Month 1   Level 1 + start Level 2     → 🔧 CLI tools, first library
📅 Month 2   Finish Level 2 + Level 3.1  → 🔀 concurrency, tested library
📅 Month 3   Level 3 (HTTP, DB, auth)    → 📋 Task API + 🔐 Auth Service
📅 Month 4   Level 4 (Proto/RPC, Redis)  → 🌉 ConnectRPC backend
📅 Month 5   Level 4 (messaging/Temporal)→ 🔄 workflow-based backend
📅 Month 6   Level 5 (perf, deploy, k8s) → 🏗️ microservices platform
♾️ Ongoing   Level 5 mastery             → 🔥 profiling, internals, OSS
```

# 🏁 Final Target

🎉 After this roadmap you can **design, build, secure, observe, deploy, and
operate** production Go systems: REST/ConnectRPC/gRPC APIs 🔌, protobuf
contracts 📜, PostgreSQL repositories 🐘, Redis caching ⚡, background workers 👷,
Temporal workflows 🔄, containerized and Kubernetes-ready services 🚢, full
observability 📊, secure auth 🔐 — and you can reason about the runtime 🧠 well
enough to make them fast. 🚀

> 💪 The roadmap ends; the craft doesn't. Keep reading source, keep shipping,
> keep mentoring. Welcome to production Go. 🐹
