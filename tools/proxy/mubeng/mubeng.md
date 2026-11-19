---
name: "mubeng"
categories:
  - blackarch-proxy
version: "254.a77f2d5"
homepage: "https://github.com/mubeng/mubeng"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2024-12-25"
build-system: go
license: APACHE
meta-checked: "2026-11-09"
github-repo: "mubeng/mubeng"
github-stars: 2800
github-pushed: "2026-09-10"
github-archived: false
github-issues: 31
github-match: "exact"
gh-checked: "2026-11-09"
worth: 5.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# mubeng

> Fast proxy checker and on-the-fly IP rotator: feed it a list of HTTP/SOCKS proxies and either validate them or run a local proxy that egresses through a different pool entry on every Nth request.

## What it's for

mubeng has two jobs: checking whether a pool of proxy IPs is alive (with geolocation of each exit), and running a local proxy server that forwards your traffic through a different proxy from the pool at a chosen rotation interval. That defeats IP-based blocking — brute-force protections, API rate limits, WAF IP bans — without extra tooling. One pool file can mix HTTP, HTTPS, SOCKS v4(A)/v5 and even AWS API Gateway endpoints, and templates (`{{USER}}`-style environment variables, `uint32` helpers) keep credentials out of the list. Point a browser, Burp Suite, OWASP ZAP or curl at the local listener; rotation, retries and dead-proxy removal are handled internally.

## Typical use cases

### Check a proxy pool and keep the live ones

```
mubeng -f proxies.txt -c --only-cc US,GB -o live.txt
```

Outcome: every entry in `proxies.txt` is checked concurrently; live proxies in the given country codes are written one-per-line to `live.txt`, ready to feed back in as a rotation pool.

### Run a rotating local proxy server

```
mubeng -a 127.0.0.1:8089 -f proxies.txt -r 10 -m random
```

Outcome: a local proxy listens on `127.0.0.1:8089` and egresses through a randomly chosen pool entry every 10 requests — point any HTTP-capable tool at it and its source IP keeps changing.

### Rotate on failure and drop dead proxies

```
mubeng -a :8089 -f proxies.txt --rotate-on-error --remove-on-error --max-errors 10
```

Outcome: failed requests are retried through the next proxy, broken entries are evicted from the pool, and rotation continues until 10 total errors have occurred.

### Chain Burp Suite through the rotator

```
mubeng -a 127.0.0.1:8089 -f proxies.txt
```

Outcome: with Burp's upstream proxy set to `127.0.0.1:8089` (Project options → Connections → Upstream Proxy Servers), all Burp traffic egresses through rotating IPs with no Burp extension — the same pattern works for OWASP ZAP's proxy-chain setting.

### Customize checker output with a template

```
mubeng -f proxies.txt -c --output-format "{{proxy}} | {{country}} | {{duration}}"
```

Outcome: check results print (and save, with `-o`) one line per proxy in the given format; available variables include `{{ip}}`, `{{city}}`, `{{org}}`, `{{region}}` and `{{hostname}}`.

### Isolate Tor circuits from one instance

```
echo "socks5://{{uint32}}:{{uint32}}@127.0.0.1:9050" > pool.txt
mubeng -f pool.txt -a :8089
```

Outcome: each outgoing connection presents a unique `USER:PASS` pair to the local Tor SOCKS port, forcing per-connection stream isolation (a distinct circuit and exit) from a single Tor instance.

*No corpus evidence — derived from upstream documentation.*
