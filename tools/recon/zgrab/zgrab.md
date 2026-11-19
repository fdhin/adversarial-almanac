---
name: "zgrab"
categories:
  - blackarch-recon
version: "804.59a517f"
homepage: "https://github.com/zmap/zgrab"
packaged-by: [blackarch]
blackarch-builddate: "2021-07-14"
build-system: go
license: Apache
meta-checked: "2026-11-09"
github-repo: "zmap/zgrab"
github-stars: 745
github-pushed: "2021-07-07"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# zgrab

> Go banner grabber that pairs with zmap: reads IPs on stdin, grabs protocol banners over plain or TLS connections, and emits JSON.

## What it's for

zgrab (ZMap project) is the application-layer half of an Internet-scale scan: zmap finds open ports, zgrab connects to each address and collects what the service says — HTTP responses, TLS certificate chains and cipher details, SSH/FTP/SMTP/POP/IMAP/telnet banners, even industrial protocols (BACnet, DNP3, Modbus, S7, Niagara Fox). Input is line-delimited hosts on stdin (or `-input-file`), output is structured JSON with per-host success/failure records, and TLS behavior is deeply configurable (client hello emulation per browser, `--heartbleed` probing, custom CA trust). It is deprecated upstream in favor of zgrab2 (archived repository), which restructures the same idea into composable per-protocol modules — new scanning infrastructure should start there; zgrab remains usable for its documented single-command form.

Commands below are verified against the upstream README.

## Typical use cases

### Banner-grab a zmap result stream over HTTPS

Collect server banners and certificates for an entire port scan.

```
zmap -p 443 --output-fields=* | ztee results.csv | zgrab --port 443 --tls --http=/ --output-file=banners.json
```

Outcome: for every address zmap found on 443, zgrab performs a TLS handshake and an HTTP GET `/`, writing one JSON record per host to `banners.json` including response headers and the parsed certificate chain; `ztee` tees the raw zmap output to `results.csv` while keeping the stream flowing.

### Grab a list of domains

Fingerprint named hosts rather than raw IPs.

```
cat domains.txt | zgrab --lookup-domain --port 443 --tls --http=/ > banners.json
```

Outcome: `--lookup-domain` tells zgrab the input lines are domain names (it resolves them itself) instead of addresses; results stream as JSON to stdout, one line per domain.

### Check a single host for Heartbleed

Test one target for the OpenSSL heartbeat vulnerability.

```
echo 198.51.100.10 | zgrab --port 443 --tls --heartbleed
```

Outcome: completes the TLS handshake and issues a malformed heartbeat request; the JSON record for the host reports whether the server is heartbleed-vulnerable. `--tls-verbose` adds the full client hello/server key exchange to the output for cipher analysis.
