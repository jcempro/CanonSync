# SYNC Engine

![Python](https://img.shields.io/badge/python-3.10%2B-blue)
![License](https://img.shields.io/badge/license-MP2%2B-green)
![Status](https://img.shields.io/badge/status-experimental-orange)
![Tests](https://img.shields.io/badge/tests-partial-yellow)
![CI](https://img.shields.io/badge/CI-not%20configured-lightgrey)
![Style](https://img.shields.io/badge/style-deterministic-informational)

![PT-BR](./README.pt-br.md)

A deterministic synchronization engine based on **state reconciliation**, **cryptographic validation**, and an **idempotent pipeline**. The project orchestrates download, cache, integrity, and post-processing under strict and predictable rules.

---

## Index

- [Overview](#overview)
- [Project Status](#project-status)
- [Architecture](#architecture)
- [`.syncdownload` Format](#syncdownload-format)
- [Design Principles](#design-principles)
- [Best Practices](#best-practices)
- [Contributing](#contributing)
- [License](#license)

---

## Overview

**SYNC Engine** addresses artifact synchronization with focus on:

- Determinism (same input → same final state)
- Integrity (SHA256/MD5 hash as source of truth)
- Efficiency (avoids downloads via cache ↔ destination reconciliation)
- Controlled extensibility (DSL + isolated subscripts)
- Synchronous execution

This is not a simple downloader: it is an **incremental decision system** with explicit semantics.

---

## Project Status

> ⚠️ **Current phase: experimental / functional validation**

- Internal API still subject to change
- Partial test coverage
- Core contracts already defined (RCF of `.syncdownload`)
- Current focus: pipeline robustness and state coherence

Production usage is **not recommended** without independent validation.

---

## Architecture

```
sync/
├── main.py
├── commons.py
├── core/
│   ├── syncdownload.parser.py
│   ├── syncdownload.processor.py
│   ├── download_manager.py
│   ├── cache_validation.py
│   ├── cleanup.py
│   ├── file_operations.py
│   ├── metadata.py
│   └── retry.py
└── utils/
    ├── progress.py
    ├── naming.py
    ├── dsl.py
    └── logging.py
```

**Separation of responsibilities is mandatory.**
No module should invade another module’s domain.

---

## `.syncdownload` Format

A declarative file that defines **source, version, integrity, and behavior**.

### Minimal structure

```
<url or DSL>
[hash]
[final_name]
[remote_hash]
[files_in_container]
>>>ext[,phase]
optional script
```

### Simple example

```
https://example.com/app.zip
d41d8cd98f00b204e9800998ecf8427e
app.zip
```

### Capabilities

- Fixed version (line 2) or dynamic version (line 4)
- DSL-based resolution (`${...}`)
- Selective extraction from containers (.zip, .tar.gz, etc.)
- Subscripts with controlled phases (`start`, `end`, etc.)

### Notes

- Line order is **semantic and mandatory**
- Encoding must be UTF-8
- Scripts are **isolated and do not interfere with the core**

---

## Design Principles

### Technical

- Separation: **HEAD ≠ GET**
- Hash as authority (not metadata)
- Hybrid cache (memory + persistent)
- Retry only for transient failures

### Execution

- Idempotent
- Deterministic
- Ordered
- No hidden side effects

### Critical rules

- Never rely solely on cache
- Never infer version from metadata
- Never treat a container as the final artifact

---

## Best Practices

### Code

- Small, specialized functions
- No logic duplication
- No hardcoding
- Prefer immutability

### Integration

- Use `download_manager` for network I/O
- Use `utils.dsl` for any dynamic resolution
- Centralize naming in `utils.naming`

### Avoid

- HTML parsing when an API exists
- Coupling between core modules
- Non-deterministic heuristics

---

## Contributing

### Requirements

- Strict adherence to project contracts
- Preserve **deterministic and diff-friendly** style
- Do not introduce implicit side effects

### Suggested workflow

1. Fork
2. Create isolated branch (`feature/...` or `fix/...`)
3. Implement minimal required changes
4. Test locally with multiple scenarios
5. Submit a concise Pull Request

### Acceptance criteria

- Consistency with system invariants
- No behavioral regression
- Structural clarity

---

## License

Distributed under the **MP2+** license.

---

## Objective

Provide a **reliable and predictable engine** for artifact synchronization, with:

- Explicit version control
- Guaranteed integrity
- Transparent and auditable pipeline

Without compromising operational simplicity.

---
