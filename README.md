# ☕ sudo-su-coffee

**Infrastructure from First Principles.**

We build infrastructure software the old-fashioned way—from scratch, in the open, with zero unnecessary dependencies. No black boxes. No supply-chain surprises. Just pure, portable, single-binary tools that both humans and AI can fully understand.

---

## 🚀 Our Philosophy

Modern infrastructure is a tower of third-party libraries. Every dependency adds complexity, security risks, and cognitive overhead.

**We do it differently:**

- **Own the Implementation** – We write our own CSV parsers, QR encoders, hash tables, and network stacks.
- **Zero Runtime Dependencies** – Every tool compiles to a single, self-contained executable.
- **AI-First Codebases** – Consistent architecture means LLMs and engineers can understand the whole stack in minutes, not days.
- **Performance Through Simplicity** – Cache-friendly, zero-allocation hot paths. Benchmarks are reproducible—not marketing fluff.

---

## 🧬 Core Languages

| Language | Purpose |
|----------|---------|
| **Zig** | Primary language for high-performance infrastructure (37+ projects) |
| **Go** | Learning and platform services (e.g., `gocloud` PaaS) |
| **Rust** | Write Robust Notification Engine |
| **Shell** | Tooling (e.g., `zigzvm`) |

---

## 📦 Ecosystem at a Glance

Our projects span the entire infrastructure stack, from storage engines to orchestration:

### Core Infrastructure
- [`zigwal`](/zigwal) – Write-ahead log (crash-safe durability)
- [`zigkv`](/zigkv) – Redis-compatible in-memory KV store
- [`zigcsv`](/zigcsv) – 2.22× faster than Pandas, zero-allocation CSV parser
- [`zigcron`](/zigcron) – Embeddable scheduler and daemon

### Networking & Protocols
- [`zigweb`](/zigweb) – HTTP reverse proxy + load balancer
- [`zigdns`](/zigdns) – DNS server with load-balancing capabilities
- [`zigsmtp`](/zigsmtp) – Full SMTP server and client

### Fintech & Logistics
- [`zigbook`](/zigbook) – Deterministic order-book matching engine
- [`zigtick`](/zigtick) – Columnar store for market tick data
- [`ziggeofence`](/ziggeofence) – Geofence event engine
- [`zigledger`](/zigledger) – Immutable double-entry accounting

### Platform & Orchestration (New!)
- [`porter`](/porter) – **Vercel for MicroVMs** – deploy lightweight, isolated VMs instantly.
- [`gocloud`](/gocloud) – **Sovereign Self-Hosted PaaS** – container orchestration and app lifecycle management.

### Observability & Security
- [`ziglog`](/ziglog) – Structured logging daemon
- [`zigmetric`](/zigmetric) – Prometheus-style metrics aggregation
- [`zigcert`](/zigcert) – Minimal ACME / Let's Encrypt client
- [`ziglock`](/ziglock) – Distributed lock service (etcd alternative)

### Tooling & VCS
- [`zigzvm`](/zigzvm) – One script, any Zig version. Instant switching.
- [`xit`](/xit) – Git-compatible VCS written in pure Zig (forked for deep integration).

*…and 20+ more projects covering job queues, full-text search, TLS termination, object storage, webhook relays, and more.*

---

## 🛠️ Shared Architectural Traits

Every repository in this organisation follows the same 10 global rules:

1. **Strict Exit Codes** – `0` success, `1` change/failure, `2` error (CI/CD ready).
2. **Streaming Processing** – No loading entire datasets into memory.
3. **Pipe Support** – All CLIs accept `-` for stdin/stdout.
4. **Dual Build Modes** – `ReleaseFast` (prod) and `ReleaseSmall` (embedded).
5. **Zero-Allocation Hot Paths** – Predictable performance, no GC pauses.
6. **Semantic Versioning** – Every release is tagged and changelogged.
7. **Embeddable Libraries** – Use as a Zig library or a standalone CLI.
8. **No Subprocesses** – Everything runs in-process. No `curl`, `sed`, or `awk` calls.
9. **Structured Outputs** – JSON, NDJSON, CSV, and plain text.
10. **Reproducible Benchmarks** – `hyperfine` comparisons against industry standards.

---

## 🎯 The Road Ahead

- Complete the missing Zig projects (all `📁 Created (empty)` placeholders).
- Turn `zigdiff` into a Git-aware diff tool using the `xit` codebase.
- Build the **control plane**: service discovery, feature flags, distributed tracing, and an API gateway—all in pure Zig.
- Continue phasing out Rust dependencies in favour of first-principles Zig implementations.

---

## 🤝 Get Involved

This is a fully open ecosystem built for longevity, auditability, and developer joy. Fork a repo, read the code, run the benchmarks, and build something solid.

**Everything is built from first principles. Everything is measurable. Everything is yours to understand.**

☕ Keep it simple. Keep it sovereign. Keep it running.

---

*Last updated: 2026*
