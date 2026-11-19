---
name: "proxify"
categories:
  - blackarch-proxy
version: "904.cb35116"
homepage: "https://github.com/projectdiscovery/proxify"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-31"
build-system: go
license: MIT
meta-checked: "2026-11-09"
github-repo: "projectdiscovery/proxify"
github-stars: 3076
github-pushed: "2026-11-04"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# proxify

> projectdiscovery's swiss-army-knife proxy: capture, filter, rewrite and dump HTTP/HTTPS (and raw TCP) traffic in one command, chain upstream proxies, and replay captured traffic into Burp.

## What it's for

Proxify is a rapid-deployment interception proxy written in Go. It MITMs HTTP/HTTPS and passes non-HTTP protocols through, dumps full request/response exchanges to JSONL, YAML or per-pair files, can match, filter and replace traffic on the fly with a DSL, rotate across upstream HTTP/SOCKS5 proxies, and even embed a DNS server with hostname overrides for traffic steering. Its companion `replay` utility re-sends a captured dump into Burp Suite (or any other proxy) so traffic captured remotely merges into local tooling exactly as originally seen. Reach for it when you need a scriptable, single-command capture point — for hand-driven interactive testing a GUI proxy like Burp or ZAP still fits better.

## Typical use cases

### Capture all HTTP/HTTPS traffic to a JSONL log

```
proxify -output capture.jsonl
```

Outcome: an HTTP proxy starts on `127.0.0.1:8888` (SOCKS5 on `127.0.0.1:10080`); every request/response pair through them is appended to `capture.jsonl` as one timestamped, URL-tagged JSON object per line. Import the CA at `http://proxify/cacert` to see decrypted HTTPS bodies.

### Store each exchange as raw files

```
proxify -store-response
```

Outcome: every request/response pair is also written under `proxify_logs/` as `{{Host}}-{{randstr}}.txt` files (alongside the JSONL log) — convenient for grepping and diffing raw traffic; `-dump-req`/`-dump-resp` narrow it to one side.

### Chain and rotate upstream proxies

```
proxify -http-proxy http://198.51.100.10:3128 -http-proxy http://198.51.100.11:3128 -c 10
```

Outcome: proxified traffic egresses through the first upstream HTTP proxy and switches to the next after every 10 requests; the same rotation works for SOCKS5 with `-socks5-proxy`.

### Rewrite traffic on the fly with the DSL

```
proxify -request-match-replace-dsl "replace(request,'firefox','chrome')"
```

Outcome: proxied requests are modified in flight — here every occurrence of `firefox` in a request becomes `chrome` before it reaches the origin server; regex replacements on responses work via `-response-match-replace-dsl "replace_regex(response, ...)"`.

### Flag interesting traffic in the dump

```
proxify -request-dsl "contains(request,'password')"
```

Outcome: exchanges matching the filter are tagged with a `.match.txt` suffix in the stored dump, separating interesting traffic from the noise without stopping capture.

### Override DNS for a hostname under test

```
proxify -dns-addr :53 -dns-mapping target.example.com:198.51.100.10
```

Outcome: an embedded DNS server on port 53 resolves `target.example.com` to the chosen address for proxify's clients while other names go upstream — handy for pinning a hostname during testing or replay.

### Replay captured traffic into Burp

```
replay -output proxify_logs/
```

Outcome: the dumped exchanges are replayed toward the destination (default `http://127.0.0.1:8080`); with Burp's upstream proxy pointed back at proxify, the entire captured history appears in Burp as if it had originally flown through it.

*No corpus evidence — derived from upstream documentation.*
