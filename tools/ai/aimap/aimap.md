---
name: "aimap"
categories:
  - blackarch-ai
version: "1.1.1"
homepage: "https://github.com/Nicholas-Kloster/aimap"
packaged-by: [blackarch]
blackarch-builddate: "2026-04-21"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "Nicholas-Kloster/aimap"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# aimap

> nmap for AI infrastructure — a single Go binary that fingerprints exposed LLM runtimes, vector databases and ML platforms across a network using read-only HTTP probes.

*No corpus evidence — derived from upstream documentation (the upstream repository has since been removed from GitHub; the archived README is the source).*

## What it's for

Finding AI services that generic scanners don't recognize. ML adoption produces shadow deployments — an Ollama instance on a dev VM, MLflow bound to 0.0.0.0, a tokenless Jupyter, an unauthenticated ChromaDB — and `nmap`/`nuclei` see only generic HTTP services. aimap fingerprints 66 AI-adjacent service types (vector databases like Qdrant/Milvus/Weaviate, LLM runtimes like vLLM/SGLang/Ollama, MLflow, Triton, LangServe, Flowise, LiteLLM, agent platforms and eval tooling) and runs deep enumerators on 33 of them that surface exposed credentials, unauthenticated RCE states and claimable admin panels. Probes are read-only, so it is safe against production hosts. It is a discovery and fingerprinting tool — findings still need manual verification.

## Typical use cases

### Audit a subnet for shadow AI services

```
aimap -target 10.0.0.0/24 -threads 50 -o audit.json
```

Outcome: every host in the CIDR is probed on aimap's default 41-port AI service list with 50 concurrent workers; the terminal shows per-service findings with risk scoring, and `audit.json` holds the machine-readable version — run it on a schedule and diff consecutive JSON reports to spot new AI services appearing.

### Deep-dive a single suspect host

```
aimap -target 10.5.5.5 -v -ports 8000,8080,8443,11434,6333,19530
```

Outcome: verbose probing of exactly the ports you care about (11434 = Ollama, 6333 = Qdrant, 19530 = Milvus, and so on), printing which AI services respond and what the deep enumerators can reach without authentication.
