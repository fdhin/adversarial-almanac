---
name: "ssh-audit"
categories:
  - scanner
homepage: "https://github.com/jtesta/ssh-audit"
github-repo: "jtesta/ssh-audit"
github-match: "exact"
origin: "awesome-pentest"
recipe: "upstream"
license: MIT
github-stars: 4346
github-pushed: "2026-11-07"
github-commits-year: 31
gh-checked: "2026-11-14"
worth: 7.8
worth-basis: "full (activity+popularity+recency+adoption)"
packaged-by: [arch, nixpkgs]
---

# ssh-audit

> SSH server and client configuration auditor — banner fingerprinting, full algorithm inventory with security ratings, policy compliance checks, and DHEat exposure testing.

## What it's for

ssh-audit (jtesta's maintained fork of the original arthepsy tool) connects to an SSH server, fingerprints the banner to identify software and OS, then enumerates every key-exchange, host-key, encryption-cipher and MAC algorithm the server offers. Each algorithm is rated against a built-in historical database (OpenSSH, Dropbear, libssh version knowledge) as removed, unsafe, weak or legacy, with concrete add/remove recommendations for the recognized software version — including Terrapin (CVE-2023-48795) exposure and post-quantum key-exchange warnings. It also audits SSH *clients* (listening on a local port and grading whoever connects), and can run compliance checks against built-in or custom hardening policies, plus the DHEat connection-rate/DoS test. It never attempts authentication and is not a brute-forcer — it is configuration analysis from handshake data only. Runs on Linux and Windows, Python 3.10+, no dependencies.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Standard server audit

```
ssh-audit target.example.com
```

Outcome: prints the banner grab, then per-section ratings (key exchanges, host keys, ciphers, MACs, compression) with failures/warnings and algorithm recommendations, plus SSH version compatibility notes; exit code 0 means every algorithm was rated secure.

### Audit a fleet of servers from a file

```
ssh-audit -T servers.txt
```

Outcome: scans every `HOST[:PORT]` entry in the file (default port 22, override the fleet default with `-p`), concurrently (`--threads`, default 32), producing one full report per host.

### Policy compliance check against a hardened baseline

```
ssh-audit -P baseline_policy.txt target.example.com
```

Outcome: instead of rating algorithms individually, runs a pass/fail comparison of the target's configuration against the policy file and prints which directives failed; get built-in policy names with `ssh-audit -L`.

### Create a baseline policy from a known-good server

```
ssh-audit -M new_policy.txt target.example.com
```

Outcome: writes the target's current algorithm configuration as a policy file you can edit and then enforce on other servers with `-P` — the ideal-config server becomes the reference.

### JSON output for pipelines

```
ssh-audit -j target.example.com > audit.json
```

Outcome: emits the full audit (fingerprints, per-algorithm findings, recommendations, notes) as machine-readable JSON on stdout instead of the colorized report.

### Audit an SSH client instead of a server

```
ssh-audit -c -p 2222
```

Outcome: opens a listener on port 2222; connect to it with `ssh -p 2222 anything@localhost` from the client machine and ssh-audit grades the client's offered algorithms (useful for jump-host or workstation hardening).

### Test DHEat CPU-exhaustion exposure

```
ssh-audit --dheat=10 target.example.com
```

Outcome: continuously performs the DHEat attack (CVE-2002-20001) with 10 concurrent sockets to demonstrate whether the server's Diffie-Hellman key exchanges can be abused to pin CPU; a standard audit's connection-rate test infers susceptibility without attacking.
