# sudo-su-coffee – Infrastructure from First Principles

## Why This Organisation Exists

Modern infrastructure is built by composing hundreds of third‑party libraries. Every dependency introduces another codebase, another API, another security surface, another release cycle, and another layer that both humans and AI systems must understand.

**Our philosophy is different.**  
We build infrastructure software from first principles using pure Zig, Go, and Rust, with a strong preference for:
- owning the implementation
- zero runtime dependencies
- zero C libraries whenever practical
- portable single binaries
- predictable memory usage
- readable source code
- embeddable libraries
- long‑term maintainability

The goal is not to reinvent software for the sake of it. The goal is to create infrastructure that is easy to understand, easy to modify, and easy for both humans and AI systems to reason about.

---

## Design Principles

### Own The Implementation  
Whenever practical we implement algorithms ourselves rather than wrapping existing C libraries. Examples include QR encoding, CSV parsing, hash tables, storage engines, network protocols, and schedulers. This provides complete understanding of the code, consistent coding style, easier optimisation, easier debugging, and fewer supply‑chain risks.

### Zero External Runtime Dependencies  
Projects compile into a single executable whenever possible. No runtime package managers, no dynamically downloaded plugins, no hidden services required for operation.

### AI‑First Codebases  
Large language models perform better when the entire system shares one architecture and one coding style. Instead of understanding hundreds of unrelated libraries, an AI assistant can understand the complete stack because every component follows the same principles. This makes maintenance, code generation, debugging, and optimisation significantly easier.

### Performance Through Simplicity  
Performance is achieved through cache‑friendly data structures, minimal allocations, predictable memory layouts, avoiding unnecessary abstractions, and measuring before optimising. Benchmarks are published only when reproducible. No synthetic marketing numbers.

### Language Strategy

| Language | Purpose | Projects |
|---|---|---|
| **Zig** | Primary — infrastructure, fintech, logistics, security | 35 projects |
| **Go** | Learning — concurrency and standard library patterns | 2 projects |
| **Rust** | Being phased out | 1 project (rewriting to Zig) |

Each project is designed to work independently while integrating naturally with the rest of the ecosystem.

---

## Benchmarking Standards & Reproducibility

Every package in this organisation follows a **strict, transparent benchmarking protocol**. Performance claims are never synthetic marketing numbers – they are reproducible, documented, and verifiable by anyone.

### The Benchmarking Philosophy

- **Compare against the industry standard** – e.g., `zigcsv` vs Pandas, `zigdiff` vs `jq` + `diff`, `zigkv` vs Redis.
- **Measure more than just speed** – we track:
  - **Average / Best / Worst time** (with variance/σ)
  - **User time & System time** (to isolate CPU vs I/O overhead)
  - **Memory usage** (peak RSS / heap allocations)
  - **Throughput** (rows/sec, requests/sec, etc.)
- **Use realistic datasets** – no synthetic micro-benchmarks. Datasets mirror real production workloads (e.g., 50M row CSVs, realistic JSON payloads, market tick data).
- **Document the exact hardware** – CPU, RAM, OS, storage (SSD/HDD), and whether running natively or under WSL/VM.
- **Provide exact commands** – using `hyperfine --warmup --min-runs` so anyone can reproduce the results.
- **Publish raw output** – show the actual terminal output (mean, σ, min, max) so there is no cherry‑picking.
- **Verify correctness** – include a verification step (e.g., `wc -l` to confirm output row count matches expected).

### Shared Benchmarking Tooling

All projects use:
- **`hyperfine`** for accurate command-line timing.
- **`/usr/bin/time -v`** or Zig's own allocator stats for memory profiling.
- **Python scripts** (where applicable) to run baseline comparisons against popular tools.
- **CI/CD benchmarks** – planned for future releases to track performance regressions.

### Mandatory Benchmark Sections in Every README

Each repository's README must include:
1. **Benchmark Configuration** – dataset size, structure, hardware.
2. **Performance Results Table** – side‑by‑side comparison with the industry alternative.
3. **Detailed Benchmark Runs** – raw `hyperfine` output.
4. **Memory Usage** – peak RSS / heap usage.
5. **Why It's Faster** – a short explanation of the architectural decisions that yield the performance gains.
6. **Reproducibility Instructions** – exact commands to regenerate the benchmarks.

This standard ensures that every package in the ecosystem is **measurably better** than existing alternatives – or it doesn't ship.

---

## Shared Architectural Features (Across All Packages)

Beyond the individual descriptions, every `sudo-su-coffee` package shares these core traits. These are the **"forgotten" features** that apply globally:

### 1. **Strict CI/CD Exit Codes**
Every CLI tool uses standardised exit codes for seamless pipeline integration:
- `0` – Success / No drift / No changes
- `1` – Changes detected / Failure / Drift found
- `2` – Error (invalid input, file not found, parsing error)

This allows gating in CI/CD pipelines without parsing unstructured output.

### 2. **Streaming / Chunked Processing**
No tool loads an entire dataset into memory. Everything is line‑by‑line, chunk‑by‑chunk, or stream‑based – guaranteeing predictable memory usage even with multi‑gigabyte inputs.

### 3. **Universal Pipe Support (`-` alias)**
Every CLI accepts `-` as a filename to read from `stdin` or write to `stdout`. This enables rich Unix‑style pipelines:
```bash
cat data.json | zigdiff base.json - | jq '.changes'
cat huge.csv | zigcsv --where "age > 30" --output - | python3 process.py
```

### 4. **Dual Build Modes**
- **`ReleaseFast`** – for maximum performance (production workloads).
- **`ReleaseSmall`** – for minimal binary size (embedded / containerised deployments).
Both are fully supported and documented.

### 5. **Zero‑Allocation Hot Paths**
Wherever possible, parsers, routers, and filters avoid heap allocations during the core processing loop. Allocation is limited to setup and result output, keeping GC pauses (or their equivalent) non‑existent.

### 6. **Semantic Versioning + Changelog**
Every release follows semantic versioning (`v1.0.0`, `v1.1.0`, etc.) with a clear changelog. The `Version` column in the repository overview tracks the latest tagged release.

### 7. **Embeddable Library + Standalone CLI**
Every project is structured as:
- **A Zig library** – importable via `build.zig.zon`.
- **A CLI binary** – built from the same codebase, using the library as its engine.
This gives users the choice of integration.

### 8. **No Subprocesses / No Shelling Out**
All operations are performed in‑process. No tool calls external commands (`git`, `curl`, `sed`, `awk`, etc.) – everything is implemented in pure Zig.

### 9. **Structured Output Formats**
Tools that produce data output support multiple machine‑readable formats:
- **JSON** (for API integration)
- **NDJSON** (for streaming pipelines)
- **Plain text / ANSI colour** (for human readability)
- **CSV** (for spreadsheet compatibility)

### 10. **Reproducible Benchmarks (as detailed above)**
Every tool includes a fully documented benchmark suite comparing it against the de‑facto standard alternative.

---

## Philosophy

Infrastructure should be:
- **understandable**
- **observable**
- **portable**
- **dependency‑light**
- **predictable**
- **benchmarked**
- **production focused**

The objective is not to replace every existing project. The objective is to build software that engineers can fully understand, confidently modify, and rely upon for the next decade.

---

# Repository Overview

| #  | Repository        | Language | Description                                                                 | Status               | Version   | Last Push     |
|----|-------------------|----------|-----------------------------------------------------------------------------|----------------------|-----------|---------------|
| 1  | sudo-su-coffee    | –        | Organisation profile README                                                 | ✅ Pushed (profile)  | –         | 9 hours ago   |
| 2  | xit               | Zig      | Full Git‑compatible VCS written in Zig – forked for study & reuse          | ✅ Pushed (forked)   | –         | 4 hours ago   |
| 3  | dispatchd-go      | Go       | Order‑rider matching daemon (concurrency learning)                          | ✅ Pushed (has code) | v0.1.0    | yesterday     |
| 4  | etaz-go           | Go       | ETA estimator (learning project)                                            | 📁 Created (empty)   | –         | 3 days ago    |
| 5  | nammapush-rs      | Rust     | gRPC push service (being rewritten to Zig)                                 | ✅ Pushed (has code) | v0.1.0    | 3 days ago    |
| 6  | zigauth           | Zig      | Identity / token issuance – secure, zero‑dependency                        | 📁 Created (empty)   | –         | yesterday     |
| 7  | zigbalance        | Zig      | TCP load balancer (non‑TLS)                                                | 📁 Created (empty)   | –         | 3 days ago    |
| 8  | zigbook           | Zig      | Deterministic order‑book matching engine                                   | 📁 Created (empty)   | –         | 3 days ago    |
| 9  | zigcert           | Zig      | Minimal ACME / Let's Encrypt client                                        | 📁 Created (empty)   | –         | 3 days ago    |
| 10 | zigcron           | Zig      | High‑performance, embeddable scheduler and lightweight CLI daemon          | ✅ Pushed (has code) | v0.1.0    | 3 days ago    |
| 11 | zigcsv            | Zig      | Zero‑allocation CSV/TSV transformer – 2.22× faster than Pandas             | ✅ Pushed (has code) | v0.1.0    | 3 days ago    |
| 12 | zigdns            | Zig      | DNS server (load balancing via DNS)                                        | 📁 Created (empty)   | –         | 3 days ago    |
| 13 | zigdiff           | Zig      | High‑performance CLI utility for structural JSON diff                      | ✅ Pushed (has code) | v0.1.0    | 3 days ago    |
| 14 | ziggeofence       | Zig      | Geofence event engine – enter/exit events for zones                       | 📁 Created (empty)   | –         | 3 days ago    |
| 15 | ziggeoidx         | Zig      | Hand‑written geospatial index (geohash/grid, nearest‑N / radius)           | 📁 Created (empty)   | –         | 3 days ago    |
| 16 | zigkv             | Zig      | Redis‑compatible in‑memory KV store, zero deps, single binary              | ✅ Pushed (has code) | v0.1.0    | yesterday     |
| 17 | zigledger         | Zig      | Immutable double‑entry accounting ledger (wallets / payments)              | 📁 Created (empty)   | –         | 3 days ago    |
| 18 | ziglock           | Zig      | Distributed lock service (etcd alternative)                                | 📁 Created (empty)   | –         | 3 days ago    |
| 19 | ziglog            | Zig      | Structured logging daemon – collect, filter, forward                       | 📁 Created (empty)   | –         | 3 days ago    |
| 20 | zigmetric         | Zig      | Metrics collection / aggregation (Prometheus‑style)                        | 📁 Created (empty)   | –         | 3 days ago    |
| 21 | zigmq             | Zig      | Lightweight pub/sub broker (NATS alternative) – fan‑out messaging          | 📁 Created (empty)   | –         | yesterday     |
| 22 | zigmux            | Zig      | SNI‑aware TCP router – multiple TLS services through one port              | 📁 Created (empty)   | –         | 3 days ago    |
| 23 | zigobj            | Zig      | S3‑compatible object storage (content‑addressed, MinIO alternative)        | 📁 Created (empty)   | –         | yesterday     |
| 24 | zigpdf            | Zig      | High‑performance PDF generation via declarative PDM templates              | ✅ Pushed (forked)   | v0.1.0    | Oct 14, 2025  |
| 25 | zigpush           | Zig      | Ultra‑optimised push service (rewrite of nammapush-rs)                     | 📁 Created (empty)   | –         | yesterday     |
| 26 | zigqr             | Zig      | High‑performance, single‑binary, zero‑dependency QR & barcode generator    | ✅ Pushed (has code) | v0.1.0    | 3 days ago    |
| 27 | zigqueue          | Zig      | Durable job queue                                                          | 📁 Created (empty)   | –         | 3 days ago    |
| 28 | zigrepl           | Zig      | Raft‑based replication daemon for multi‑node KV/WAL                       | 📁 Created (empty)   | –         | yesterday     |
| 29 | zigriskguard      | Zig      | Gig‑specific fraud / abuse checks                                          | 📁 Created (empty)   | –         | yesterday     |
| 30 | zigriskgate       | Zig      | Pre‑trade risk check – rule‑based, sub‑ms latency (GC‑free)               | 📁 Created (empty)   | –         | 3 days ago    |
| 31 | zigsearch         | Zig      | BM25 full‑text search engine                                               | 📁 Created (empty)   | –         | 3 days ago    |
| 32 | zigshot           | Zig      | Copy‑on‑write snapshot tool (backup primitive)                             | 📁 Created (empty)   | –         | 3 days ago    |
| 33 | zigsmtp           | Zig      | Full email system (inbound + outbound, own SMTP)                           | 📁 Created (empty)   | –         | yesterday     |
| 34 | zigtick           | Zig      | Append‑only columnar store for market tick data (backtesting / charting)   | 📁 Created (empty)   | –         | 3 days ago    |
| 35 | zigtls            | Zig      | TLS termination + certificate management                                   | 📁 Created (empty)   | –         | 3 days ago    |
| 36 | zigvault          | Zig      | Fast, secure environment access – encryption‑at‑rest, zero‑auth friction  | 📁 Created (empty)   | –         | yesterday     |
| 37 | zigwal            | Zig      | Write‑ahead log (WAL) – crash‑safe durability layer                        | ✅ Pushed (has code) | v0.1.0    | 5 hours ago   |
| 38 | zigweb            | Zig      | HTTP reverse proxy + load balancing                                        | 📁 Created (empty)   | –         | 3 days ago    |
| 39 | zigwebhookd       | Zig      | Webhook relay                                                              | 📁 Created (empty)   | –         | 3 days ago    |
| 40 | zigzvm            | Shell    | One script, any Zig version – instant version switching                    | ✅ Pushed (has code) | v0.1.0    | 9 hours ago   |

*Note: The “Version” column indicates the latest tagged release. Repos marked “–” are either empty placeholders, not yet versioned, or forked external projects that haven't been tagged by us.*

---

# Detailed Project Overview

Below is a comprehensive description of every project, grouped by domain. Each entry explains **what it does**, **why it exists**, and **how it fits into the ecosystem**.

---

## Core Infrastructure

### zigwal – Write‑Ahead Log (WAL) Primitive
**Repo:** [zigwal](https://github.com/sudo-su-coffee/zigwal)  
**Status:** ✅ Pushed (v0.1.0)  
**What it is:** A crash‑safe durability layer – the foundation for any storage engine that needs to survive power failures.  
**Why:** Instead of relying on SQLite or RocksDB’s WAL, we own the implementation to keep it minimal, predictable, and tightly integrated with our own KV and queue stores.

### zigkv – Redis‑Compatible In‑Memory KV Store
**Repo:** [zigkv](https://github.com/sudo-su-coffee/zigkv)  
**Status:** ✅ Pushed (v0.1.0)  
**What it is:** A pure‑Zig key‑value store that speaks the Redis protocol (so you can drop it in as a drop‑in replacement) but with zero C dependencies and a single binary.  
**Why:** Provides a fast, embeddable cache and state store without the overhead of a full Redis server.

### zigrepl – Raft‑Based Replication Daemon
**Repo:** [zigrepl](https://github.com/sudo-su-coffee/zigrepl)  
**Status:** 📁 Created (empty) – planned  
**What it is:** Multi‑node replication for KV and WAL using the Raft consensus algorithm.  
**Why:** Turn a single‑node zigkv into a highly available cluster.

### zigqueue – Durable Job Queue
**Repo:** [zigqueue](https://github.com/sudo-su-coffee/zigqueue)  
**Status:** 📁 Created (empty) – planned  
**What it is:** A persistent job queue with at‑least‑once delivery, ideal for background processing.  
**Why:** Many systems need reliable task scheduling without pulling in RabbitMQ or Redis.

### zigcron – Embeddable Scheduler / CLI Daemon
**Repo:** [zigcron](https://github.com/sudo-su-coffee/zigcron)  
**Status:** ✅ Pushed (v0.1.0)  
**What it is:** A high‑performance, embeddable cron‑like scheduler that can be embedded in your application or run as a standalone daemon.  
**Why:** Avoid cron’s limitations and enjoy a single‑binary, zero‑dependency scheduling solution.

### zigcsv – Zero‑Allocation CSV/TSV Transformer
**Repo:** [zigcsv](https://github.com/sudo-su-coffee/zigcsv)  
**Status:** ✅ Pushed (v0.1.0)  
**What it is:** The ultimate high‑performance, zero‑allocation CSV/TSV transformer and universal data bridge – bringing Pandas‑grade power and flexibility to any language pipeline at native C‑speed.  
**Why:** Data pipelines often bottleneck on parsing; this provides a universal bridge for any language.

### zigdiff – Semantic JSON Diff (Git‑Aware)
**Repo:** [zigdiff](https://github.com/sudo-su-coffee/zigdiff)  
**Status:** ✅ Pushed (v0.1.0)  
**What it is:** A high‑performance CLI utility and library for structural, semantic comparison of JSON documents – detects drift, config changes, and schema evolution. **Future versions will integrate Git object parsing (using the `xit` fork) to compare commits and trees.**  
**Why:** Essential for infrastructure‑as‑code and configuration management; Git awareness makes it invaluable for auditing history and rolling back.

### zigshot – Copy‑on‑Write Snapshot Tool
**Repo:** [zigshot](https://github.com/sudo-su-coffee/zigshot)  
**Status:** 📁 Created (empty) – planned  
**What it is:** A backup primitive that uses copy‑on‑write to create point‑in‑time snapshots of data.  
**Why:** Foundation for backup systems without relying on LVM or ZFS.

---

## Networking & Protocols

### zigweb – HTTP Reverse Proxy + Load Balancer
**Repo:** [zigweb](https://github.com/sudo-su-coffee/zigweb)  
**Status:** 📁 Created (empty) – planned  
**What it is:** A modern HTTP/1.1 and HTTP/2 reverse proxy with built‑in load balancing and health checks.  
**Why:** Nginx is complex; this is a simpler, more auditable alternative.

### zigtls – TLS Termination + Certificate Management
**Repo:** [zigtls](https://github.com/sudo-su-coffee/zigtls)  
**Status:** 📁 Created (empty) – planned  
**What it is:** Handles TLS termination and automated certificate renewal (via ACME) in a single binary.  
**Why:** Reduces the need for separate tools like certbot and HAProxy.

### zigmux – SNI‑Aware TCP Router
**Repo:** [zigmux](https://github.com/sudo-su-coffee/zigmux)  
**Status:** 📁 Created (empty) – planned  
**What it is:** Routes multiple TLS services through one port by inspecting the SNI extension, without decrypting traffic.  
**Why:** Efficiently host dozens of domains on a single IP without a dedicated load balancer.

### zigbalance – TCP Load Balancer (non‑TLS)
**Repo:** [zigbalance](https://github.com/sudo-su-coffee/zigbalance)  
**Status:** 📁 Created (empty) – planned  
**What it is:** A pure‑TCP layer‑4 load balancer.  
**Why:** For services that don’t use TLS, this provides simple round‑robin or least‑connections distribution.

### zigdns – DNS Server (Load Balancing via DNS)
**Repo:** [zigdns](https://github.com/sudo-su-coffee/zigdns)  
**Status:** 📁 Created (empty) – planned  
**What it is:** An authoritative DNS server that can return different IPs based on load or geo‑location.  
**Why:** DNS‑based load balancing can be simpler and more resilient than layer‑7 proxies for certain workloads.

### zigsmtp – Full Email System
**Repo:** [zigsmtp](https://github.com/sudo-su-coffee/zigsmtp)  
**Status:** 📁 Created (empty) – planned  
**What it is:** A complete SMTP server and client – inbound + outbound, with own implementation of the protocol.  
**Why:** Email is still critical; having a lightweight, embeddable SMTP stack reduces dependency on Postfix or Sendmail.

### zigmq – Lightweight Pub/Sub Broker
**Repo:** [zigmq](https://github.com/sudo-su-coffee/zigmq)  
**Status:** 📁 Created (empty) – planned  
**What it is:** A NATS‑style fan‑out messaging broker with topics, subscriptions, and persistent queues.  
**Why:** For microservices communication without the overhead of Kafka or RabbitMQ.

### zigobj – S3‑Compatible Object Storage
**Repo:** [zigobj](https://github.com/sudo-su-coffee/zigobj)  
**Status:** 📁 Created (empty) – planned  
**What it is:** A content‑addressed object store that speaks the S3 API (but not a full spec clone – “protocol‑compatible, not spec‑complete”).  
**Why:** Provides a lightweight MinIO alternative for private cloud storage.

---

## Observability

### ziglog – Structured Logging Daemon
**Repo:** [ziglog](https://github.com/sudo-su-coffee/ziglog)  
**Status:** 📁 Created (empty) – planned  
**What it is:** Collects, filters, and forwards logs in a structured format (JSON, key‑value).  
**Why:** Centralised logging without the complexity of ELK or Loki – just a single binary.

### zigmetric – Metrics Collection / Aggregation
**Repo:** [zigmetric](https://github.com/sudo-su-coffee/zigmetric)  
**Status:** 📁 Created (empty) – planned  
**What it is:** Exposes Prometheus‑style metrics and aggregates counters, gauges, and histograms.  
**Why:** Lightweight metrics endpoint for any service, no need for a separate exporter.

---

## Security

### zigcert – Minimal ACME / Let's Encrypt Client
**Repo:** [zigcert](https://github.com/sudo-su-coffee/zigcert)  
**Status:** 📁 Created (empty) – planned  
**What it is:** A simple CLI to obtain and renew TLS certificates from Let’s Encrypt.  
**Why:** Certbot is heavy; this does the same in a few kilobytes.

### ziglock – Distributed Lock Service
**Repo:** [ziglock](https://github.com/sudo-su-coffee/ziglock)  
**Status:** 📁 Created (empty) – planned  
**What it is:** An etcd‑style distributed lock manager, using Raft (via zigrepl) under the hood.  
**Why:** Coordinate critical sections across multiple nodes without running a full etcd cluster.

### zigauth – Identity / Token Issuance
**Repo:** [zigauth](https://github.com/sudo-su-coffee/zigauth)  
**Status:** 📁 Created (empty) – planned  
**What it is:** A zero‑dependency identity provider that issues claims‑based tokens (JWT or custom).  
**Why:** Authentication should be simple and auditable – no OAuth2 complexity if you don’t need it.

### zigvault – Fast, Secure Environment Access
**Repo:** [zigvault](https://github.com/sudo-su-coffee/zigvault)  
**Status:** 📁 Created (empty) – planned  
**What it is:** Encryption‑at‑rest with zero‑auth friction – secrets are decrypted on‑the‑fly with military‑grade security.  
**Why:** Safer than plaintext env files and simpler than HashiCorp Vault.

---

## Data & Storage

### zigsearch – BM25 Full‑Text Search Engine
**Repo:** [zigsearch](https://github.com/sudo-su-coffee/zigsearch)  
**Status:** 📁 Created (empty) – planned  
**What it is:** A search engine that uses the BM25 ranking algorithm, with inverted indexes.  
**Why:** Lightweight alternative to Elasticsearch – perfect for small to medium datasets.

### zigwebhookd – Webhook Relay
**Repo:** [zigwebhookd](https://github.com/sudo-su-coffee/zigwebhookd)  
**Status:** 📁 Created (empty) – planned  
**What it is:** A reliable webhook delivery service with retries, deduplication, and signing.  
**Why:** Webhooks are everywhere; having a dedicated relay improves reliability.

---

## Fintech

### zigbook – Deterministic Order‑Book Matching Engine
**Repo:** [zigbook](https://github.com/sudo-su-coffee/zigbook)  
**Status:** 📁 Created (empty) – planned  
**What it is:** A pure‑Zig order‑book that matches bids and asks with deterministic behaviour (no randomness).  
**Why:** Core for any exchange or trading platform – built from scratch for low latency and auditability.

### zigtick – Append‑Only Columnar Store for Market Ticks
**Repo:** [zigtick](https://github.com/sudo-su-coffee/zigtick)  
**Status:** 📁 Created (empty) – planned  
**What it is:** Stores tick‑by‑tick market data in an append‑only columnar format, feeding backtesting, charting, and analytics.  
**Why:** Financial time‑series data demands high write throughput and efficient retrieval – this is designed for that.

### zigledger – Immutable Double‑Entry Accounting Ledger
**Repo:** [zigledger](https://github.com/sudo-su-coffee/zigledger)  
**Status:** 📁 Created (empty) – planned  
**What it is:** Core accounting primitive – tracks wallets, payments, and settlements with double‑entry integrity.  
**Why:** Every financial system needs a reliable ledger; this one is simple, auditable, and embeddable.

### zigriskgate – Pre‑Trade Risk Check
**Repo:** [zigriskgate](https://github.com/sudo-su-coffee/zigriskgate)  
**Status:** 📁 Created (empty) – planned  
**What it is:** A rule‑based risk engine that evaluates orders before they hit the matching engine – sub‑millisecond latency, GC‑free.  
**Why:** Risk checks are mandatory for regulated trading; this provides predictable performance.

---

## Logistics

### ziggeofence – Geofence Event Engine
**Repo:** [ziggeofence](https://github.com/sudo-su-coffee/ziggeofence)  
**Status:** 📁 Created (empty) – planned  
**What it is:** Watches position streams and fires enter/exit events when a device crosses predefined zones.  
**Why:** Essential for delivery tracking, fleet management, and location‑based services.

### ziggeoidx – Hand‑Written Geospatial Index
**Repo:** [ziggeoidx](https://github.com/sudo-su-coffee/ziggeoidx)  
**Status:** 📁 Created (empty) – planned  
**What it is:** A custom geohash/grid index that supports nearest‑neighbour and radius searches – no H3 or S2 dependencies.  
**Why:** Avoids large third‑party libraries; gives full control over precision and performance.

### zigriskguard – Gig‑Specific Fraud / Abuse Checks
**Repo:** [zigriskguard](https://github.com/sudo-su-coffee/zigriskguard)  
**Status:** 📁 Created (empty) – planned  
**What it is:** Checks for fraudulent or abusive behaviour in gig‑economy platforms (e.g., fake riders, duplicate orders).  
**Why:** Trust and safety are critical; this builds a dedicated check engine.

### zigpush – Ultra‑Optimised Push Notification Service
**Repo:** [zigpush](https://github.com/sudo-su-coffee/zigpush)  
**Status:** 📁 Created (empty) – planned  
**What it is:** A rewrite of the Rust `nammapush-rs` service – handles 100k+ persistent WebSocket/SSE streams with minimal CPU.  
**Why:** Delivering real‑time notifications to mobile and web clients at scale.

---

## Go Projects (Learning)

### dispatchd-go – Order‑Rider Matching Daemon
**Repo:** [dispatchd-go](https://github.com/sudo-su-coffee/dispatchd-go)  
**Status:** ✅ Pushed (v0.1.0)  
**What it is:** A Go daemon that matches orders to nearby riders using a simple scoring algorithm.  
**Why:** Kept as a reference for learning Go’s concurrency patterns – will eventually be rewritten in Zig.

### etaz-go – ETA Estimator
**Repo:** [etaz-go](https://github.com/sudo-su-coffee/etaz-go)  
**Status:** 📁 Created (empty) – planned  
**What it is:** A Go library to estimate travel times based on traffic data.  
**Why:** Learning exercise; may be replaced by a Zig equivalent.

---

## Rust Project (Being Rewritten)

### nammapush-rs – gRPC Push Service
**Repo:** [nammapush-rs](https://github.com/sudo-su-coffee/nammapush-rs)  
**Status:** ✅ Pushed (v0.1.0) – being phased out  
**What it is:** A lightning‑fast, ultra‑optimised gRPC service for push notifications, supporting 100k+ persistent streams.  
**Why:** Rewriting to Zig (`zigpush`) to eliminate Rust’s dependencies and achieve even lower latency.

---

## Tooling

### zigzvm – Zig Version Manager
**Repo:** [zigzvm](https://github.com/sudo-su-coffee/zigzvm)  
**Status:** ✅ Pushed (v0.1.0)  
**What it is:** One script. Any Zig version. Instant switching.  
**Why:** Makes it easy to test against multiple Zig releases without manual downloads.

---

## Forked / External

### xit – Git‑Compatible VCS & Reusable Git Library
**Repo:** [xit](https://github.com/sudo-su-coffee/xit)  
**Status:** ✅ Pushed (forked) – no version tag yet  
**What it is:** A full, Git‑compatible version control system written in pure Zig. It supports the Git network protocol (HTTP, SSH, raw), patch‑based merging, chunked storage for large files, and an immutable database. Crucially, it contains a **reusable Git implementation** that can be used as a library in other Zig projects.  
**Why:** We forked this to study its internals and eventually adapt parts of its Git object parsing, commit traversal, and networking into our own tools – particularly `zigdiff`, which will become Git‑aware. This aligns with our “own the implementation” philosophy: we now have full control over the code, can customise it, and contribute improvements back.

### zigpdf – PDF Generation via PDM Templates
**Repo:** [zigpdf](https://github.com/sudo-su-coffee/zigpdf)  
**Status:** ✅ Pushed (forked) – v0.1.0  
**What it is:** High‑performance PDF generation system using declarative PDM templates.  
**Why:** Forked from an existing project to maintain and extend it under our philosophy.

---

# Future Roadmap

- All `📁 Created (empty)` projects will be implemented in Zig over the coming months.
- Every project will eventually have reproducible benchmarks and be tagged with semantic versions.
- The Go and Rust projects will be fully replaced by Zig equivalents.
- **`zigdiff`** will leverage the `xit` fork to add Git object comparison, commit history diffing, and semantic rollback capabilities.

---

*This document is a living guide to the sudo‑su‑coffee ecosystem. Updated regularly as projects evolve.*
