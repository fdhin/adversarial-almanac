---
name: "sulla"
categories:
  - scanner
homepage: "https://github.com/praetorian-inc/Sulla"
origin: "hacktricks"
recipe: "upstream"
packaged-by: []
build-system: go
meta-checked: "2026-11-13"
github-repo: "praetorian-inc/Sulla"
github-stars: 40
github-archived: false
github-match: "exact"
gh-checked: "2026-11-13"
worth: 5.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-13"
license: "Apache2"
---

# sulla

> Praetorian's SMB share scanner: discovers readable shares across an Active Directory domain and sweeps them for secrets with Titus (NoseyParker-style) rules.

## What it's for

Misconfigured SMB shares are one of the cheapest paths to domain compromise — but finding the one juicy file among thousands of hosts needs automation. Sulla does the whole loop from one Go binary: it finds domain controllers over DNS SRV records (optionally via LDAP/LDAPS), enumerates every share your credentials can read (filtering disabled accounts and stale machines, Snaffler-style), then scans the contents with Titus — a Go port of NoseyParker — for API keys, passwords and certificates, reporting in txt/json/jsonl/SARIF. Reach for it during AD internal assessments or purple-team validation of share hygiene. It only reads shares (no write primitives), reports only accessible shares, and quick mode (default) restricts scanning to high-value file types so domain-wide runs finish; `--full` trades time for coverage. It complements rather than replaces Snaffler-style tools with its SOCKS5-native pivoting and SARIF output.

## Typical use cases

### Discover and scan every readable share in a domain

```
sulla -u admin -p secret123 -d corp.local -o results/ -of txt,json
```

Outcome: Sulla locates a DC via `_ldap._tcp.dc._msdcs` SRV records (falling back to the next on failure), enumerates shares readable by the credentials, and Titus-scans each one in quick mode; findings land in `results/` as both human-readable text and JSON — one file per share by default, `-z` to zip everything into one archive.

### Discovery only, review before scanning

```
sulla -u admin -p secret123 -d corp.local -do -o
```

Outcome: prints the UNC paths of every accessible share without scanning any content — an inventory to review or filter by hand, later fed back with `--target-file` (`host,share` CSV or `\\host\share$` lines) for the actual Titus run.

### Scan one known share

```
sulla -h fileserver.corp.local -s SYSVOL -u admin -p secret123 -d corp.local -o results/
```

Outcome: skips discovery entirely and secret-scans the named share — the surgical mode when a target is already known; anonymous access works by omitting credentials (`sulla -h 192.168.1.100 -s public`).

### Pivot into the network through SOCKS5

```
ssh -D 1080 -N pivot-host
sulla --socks5 127.0.0.1:1080 -dns 10.0.0.10 -u admin -p secret -d corp.local -do
```

Outcome: all SMB/LDAP/DNS traffic tunnels through the SSH dynamic forward (Go binaries ignore `proxychains`/LD_PRELOAD, hence the native flag); because SOCKS can't carry the UDP SRV query, `-dns 10.0.0.10` tunnels DNS over TCP to the internal resolver so domain auto-discovery still works — or name a controller directly with `-dc dc01.corp.local` when no internal DNS is reachable.

### Scan for organization-specific secrets

```
sulla -h fileserver.corp.local -s Data --custom-rules ./acme-token.yml --custom-rules-only
```

Outcome: Titus runs with only your YAML rule (`pattern`, `id`, `base_score` 0-100, one rule per file; `-cro` omitted makes custom rules additive to the built-in set) — catching token formats generic rulesets don't know; `--keywords` can likewise force-include filenames the default exclusions would skip.

*No corpus evidence — derived from upstream documentation.*
