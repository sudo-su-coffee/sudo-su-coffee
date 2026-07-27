# sudo-su-coffee

**Infrastructure software, built from scratch.**

## Why This Organisation Exists

Modern infrastructure is built by stacking hundreds of third-party libraries on top of each other. Every dependency is another codebase you didn't write, another API you don't control, another security surface, another release cycle, another thing that can break underneath you.

**We don't do that here.**

We build infrastructure software from first principles, in pure Zig, Go, and Rust — no wrappers, no bindings to someone else's C library, no framework doing the real work behind the scenes. If it's core to the stack, we write it ourselves.

Preferences, in order:
- own the implementation
- zero runtime dependencies
- zero C libraries wherever practical
- portable single binaries
- predictable memory usage
- readable source, no cleverness for its own sake
- embeddable as a library, not just a daemon
- built to still make sense in ten years

The point isn't reinventing things for sport. The point is that every piece of this stack is something we can fully read, fully modify, and fully trust — because we wrote it.

---

## Design Principles

### Own the implementation

QR encoding, CSV parsing, hash tables, storage engines, network protocols, schedulers — we write the algorithm, not wrap someone else's library for it. That means full understanding of the code, a consistent style across the whole stack, easier debugging, and no supply-chain risk from a dependency we don't control.

### Zero external runtime dependencies

Every project should compile to a single executable. No package manager pulling things in at runtime, no plugins fetched from somewhere else, no hidden service required just to boot.

### Performance through simplicity

Cache-friendly layouts, minimal allocations, predictable memory — not abstraction for abstraction's sake. Benchmarks only get published when they're reproducible. No marketing numbers.

### Build primitives, not frameworks

Small, focused tools that compose. Every piece works standalone.

```
Applications
        │
        ▼
 zigweb ─ zigqueue ─ zigsearch ─ zigsmtp
        │
        ▼
      zigkv
        │
   ┌────┴────┐
   │         │
zigwal    zigobj
```

---

## Repositories (39)

### Shipped — has working code

| Project | What it is |
|---|---|
| **zigqr** | QR/barcode generator, own encoding algorithm, no C lib |
| **zigdiff** | Semantic JSON diff — structural comparison, config-drift detection |
| **zigcron** | Embeddable scheduler and lightweight CLI daemon |
| **zigcsv** | Zero-allocation CSV/TSV toolkit — measured faster and far lighter on memory than Pandas on tested datasets |
| **zigkv** | In-memory key-value store, pure Zig, Redis-compatible, zero deps, single binary |
| **dispatchd-go** | Order-rider matching daemon |
| **etaz-go** | ETA estimator |
| **nammapush-rs** | gRPC-based push-notification service, 100k+ persistent streams (rewrite target: `zigpush`) |
| **zigzvm** | One script, any Zig version, instant switching |

### In progress — repo live, building it out

| Project | What it is |
|---|---|
| **zigobj** | S3-compatible object storage daemon, content-addressed, protocol-compatible not spec-complete |
| **zigauth** | Identity/token issuance, claims-based, zero-dependency |
| **zigvault** | Encrypted-at-rest environment/secrets access, zero-auth friction |
| **zigriskguard** | Gig-platform fraud/abuse checks |
| **zigpush** | Ultra-optimized push notifications, pure Zig (rewrite of `nammapush-rs`) |
| **zigsmtp** | Full email system, own SMTP implementation |
| **zigrepl** | Raft-based replication daemon for multi-node KV/WAL |
| **zigmq** | Lightweight pub/sub broker — NATS alternative |
| **ziggeoidx** | Hand-written geospatial index, own geohash/grid algorithm, no H3/S2 |
| **zigriskgate** | Pre-dispatch risk check gate, rule-based, sub-ms, GC-free |
| **zigwebhookd** | Webhook relay |
| **zigqueue** | Durable job queue |
| **zigsearch** | BM25 full-text search engine |
| **zigmetric** | Metrics collection/aggregation, Prometheus-style |
| **ziglog** | Structured logging daemon |
| **ziggeofence** | Geofence event engine — enter/exit events off position streams |
| **zigtick** | Append-only columnar store for market tick data |
| **zigledger** | Immutable double-entry accounting ledger |
| **zigbook** | Deterministic order-book matching engine |
| **zigcert** | Minimal ACME/Let's Encrypt client — certbot alternative |
| **ziglock** | Distributed lock service — etcd alternative |
| **zigshot** | Copy-on-write snapshot tool |
| **zigdns** | DNS server, load-balance by DNS |
| **zigweb** | HTTP reverse proxy + load balancing |

---

## Language Strategy

| Language | Role |
|---|---|
| **Zig** | Primary — infra, fintech, logistics, security. Bulk of the stack. |
| **Go** | Kept for `dispatchd-go` / `etaz-go` — concurrency and stdlib patterns. |
| **Rust** | `nammapush-rs` being phased out, rewritten in pure Zig as `zigpush`. |

---

## Benchmarks

Performance claims are backed by reproducible benchmarks only.

Currently published: `zigcsv` shows significantly lower memory usage than Pandas during CSV processing, with substantially higher read throughput on tested datasets.

Methodology, datasets, and commands are published alongside each project. No numbers without a reproducible procedure behind them.

---

## Long-Term Vision

Not one big framework — a full set of infrastructure primitives, each standalone, each composable:

key-value storage, object storage, queues, search, logging, metrics, replication, networking, scheduling, identity, messaging.

`zigkv` is the anchor piece — everything above it in the stack (`zigweb`, `zigqueue`, `zigsearch`, `zigsmtp`) builds on it without pulling in a third-party runtime underneath.

---

## Philosophy

Infrastructure should be:
understandable, observable, portable, dependency-light, predictable, benchmarked, production-focused.

We're not trying to replace every existing project out there. We're trying to build software that we — and anyone else who picks it up — can fully understand, confidently modify, and rely on for the next decade.
