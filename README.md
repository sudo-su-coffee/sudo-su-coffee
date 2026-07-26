# sudo-su-coffee

> **AI-native infrastructure software written from first principles.**

## Why this organisation exists

Modern infrastructure is built by composing hundreds of third-party libraries.

Every dependency introduces another codebase, another API, another security surface, another release cycle, and another layer that both humans and AI systems must understand.

Our philosophy is different.

We build infrastructure software from first principles using **pure Zig**, **Go**, and **Rust**, with a strong preference for:

* owning the implementation
* zero runtime dependencies
* zero C libraries whenever practical
* portable single binaries
* predictable memory usage
* readable source code
* embeddable libraries
* long-term maintainability

The goal is not to reinvent software for the sake of it.

The goal is to create infrastructure that is easy to understand, easy to modify, and easy for both humans and AI systems to reason about.

---

# Design Principles

Every project follows the same engineering philosophy.

## Own the implementation

Whenever practical we implement algorithms ourselves rather than wrapping existing C libraries.

Examples include:

* QR encoding
* CSV parsing
* hash tables
* storage engines
* network protocols
* schedulers

This provides:

* complete understanding of the code
* consistent coding style
* easier optimisation
* easier debugging
* fewer supply-chain risks

---

## Zero external runtime dependencies

Projects should compile into a single executable whenever possible.

No runtime package managers.

No dynamically downloaded plugins.

No hidden services required for operation.

---

## AI-first codebases

Large language models perform better when the entire system shares one architecture and one coding style.

Instead of understanding hundreds of unrelated libraries, an AI assistant can understand the complete stack because every component follows the same principles.

This makes maintenance, code generation, debugging and optimisation significantly easier.

---

## Performance through simplicity

Performance is achieved through:

* cache-friendly data structures
* minimal allocations
* predictable memory layouts
* avoiding unnecessary abstractions
* measuring before optimising

Benchmarks are published only when reproducible.

No synthetic marketing numbers.

---

## Build primitives, not frameworks

Small focused tools compose into larger systems.

```
Applications
        │
        ▼
 zigweb
 zigqueue
 zigsearch
 zigsmtp
        │
        ▼
      zigkv
        │
   ┌────┴────┐
   │         │
zigwal    zigobj
```

Every component should be usable independently.

---

# Current Projects

## Zig

### Core

* zigcsv
* zigqr
* zigdiff
* zigcron
* zigkv
* zigwal
* zigqueue
* zigsearch
* zigweb
* zigtls
* zigmux
* zigdns
* zigmetric
* ziglog
* zigcert
* zigbalance
* ziglock
* zigwebhookd

### Data & Storage

* zigobj
* zigshot

### Fintech

* zigledger
* zigbook
* zigtick

### Logistics

* ziggeofence
* ziggeoidx

---

## Go

Focused on distributed systems, orchestration and business services.

* dispatchd-go
* etaz-go
* authd-go
* tripstate-go
* driftctl-lite-go
* envsafe-go
* gitstatd-go
* sqlreplay-go
* logshape-go
* riskguard-go

---

## Rust

Reserved for workloads where the ecosystem or deterministic latency provides a clear advantage.

* nammapush-rs
* riskgate-rs
* parqtail-rs

---

# Current Status

Several projects are already operational and publicly available.

Examples include:

* zigcsv
* zigqr
* zigdiff
* zigcron
* nammapush-rs

Additional repositories are currently under active development.

---

# Benchmarks

Performance claims are backed by reproducible benchmarks.

Current observations include:

* **zigcsv** demonstrates significantly lower memory usage than Pandas during CSV processing.
* Internal benchmarking also shows substantially higher CSV read throughput on tested datasets.

Benchmark methodology, datasets and commands are published alongside each project.

No benchmark numbers are presented without reproducible test procedures.

---

# Long-Term Vision

Rather than creating one large framework, this organisation aims to build a complete collection of infrastructure primitives.

Examples include:

* key-value storage
* object storage
* queues
* search
* logging
* metrics
* replication
* networking
* scheduling
* identity
* messaging

Each project is designed to work independently while integrating naturally with the rest of the ecosystem.

---

# Philosophy

Infrastructure should be:

* understandable
* observable
* portable
* dependency-light
* predictable
* benchmarked
* production focused

The objective is not to replace every existing project.

The objective is to build software that engineers can fully understand, confidently modify, and rely upon for the next decade.
