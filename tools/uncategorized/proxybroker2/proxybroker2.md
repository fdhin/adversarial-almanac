---
name: "proxybroker2"
categories:
version: "663.bbf753d"
homepage: "https://github.com/bluet/proxybroker2"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-25"
build-system: python
license: Apache
meta-checked: "2026-11-09"
github-repo: "bluet/proxybroker2"
github-stars: 1033
github-pushed: "2026-11-06"
github-archived: false
github-issues: 64
github-match: "search"
gh-checked: "2026-11-09"
worth: 7.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 115
gh-activity-checked: "2026-11-09"
---

# proxybroker2

> Asynchronously find, check and serve public HTTP(S) and SOCKS proxies — the maintained successor to the abandoned ProxyBroker.

## What it's for

ProxyBroker2 gathers public proxy addresses from ~50 sources, validates them against judge servers, and can then act as a local proxy server that routes your traffic through the working pool with automatic rotation. Supported protocols: HTTP(S), SOCKS4/5, plus CONNECT to ports 80 and 23; proxies can be filtered by type, anonymity level, response time, country and DNSBL status. The CLI entry point is the `proxybroker` command (also usable as `python -m proxybroker`) with subcommands `find` (search and validate), `grab` (save a checked pool to file), `serve` (local rotating proxy endpoint) and `update-geo` (refresh GeoIP data). The BlackArch package builds the bluet/proxybroker2 fork and replaces the old `proxybroker` package.

## Typical use cases

### Find working proxies with filters

```
proxybroker find --types HTTP HTTPS --lvl High --strict --limit 20
```

Outcome: queries all providers, validates candidates strictly, and prints high-anonymity HTTP/HTTPS proxies as they are confirmed, up to 20 — add `--countries US` or `--dnsbl` to narrow further.

### Grab a checked pool to file

```
proxybroker grab --socks5 --limit 50 --outfile proxies.txt
```

Outcome: collects and validates 50 working SOCKS5 proxies and writes them to `proxies.txt`, ready for offline consumption by other tooling.

### Serve a local rotating proxy

```
proxybroker serve --host 127.0.0.1 --port 8888 --types HTTP HTTPS --lvl High --min-queue 5
```

Outcome: starts a local proxy endpoint on 127.0.0.1:8888 kept stocked with at least five validated proxies; point tools at it as their HTTP proxy (`http_proxy=http://127.0.0.1:8888 curl http://example.com`) and each request is forwarded through a different external proxy.

*No corpus evidence — derived from upstream documentation.*
