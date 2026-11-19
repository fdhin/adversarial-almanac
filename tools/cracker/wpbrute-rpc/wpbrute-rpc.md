---
name: "wpbrute-rpc"
categories:
  - blackarch-cracker
version: "3.e7d8145"
homepage: "https://github.com/zendoctor/wpbrute-rpc"
packaged-by: [blackarch]
blackarch-builddate: "2025-04-29"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "zendoctor/wpbrute-rpc"
github-stars: 71
github-pushed: "2015-10-19"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wpbrute-rpc

> WordPress brute forcer that uses the XML-RPC `system.multicall` amplification to test hundreds of passwords per single HTTP request.

*No corpus evidence — derived from upstream documentation.*

## What it's for

wpbrute-rpc (Ruby) exploits WordPress's `xmlrpc.php` multicall interface to
bypass login rate limiting: instead of one password per request against
`wp-login.php` (trivially throttleable), each request packs up to a
thousand credential attempts into one `system.multicall` batch, so a whole
dictionary crosses in a handful of requests. The target URL must be the
full RPC endpoint ending in `xmlrpc.php`, and the password list is a plain
newline-separated file. On success it prints the recovered password. It is
strictly a password-spraying proof-of-concept for the multicall weakness —
defended sites simply block `xmlrpc.php`, and there is no fallback to
form login.

## Typical use cases

### Run an amplified multicall brute force

Batch many password attempts into each XML-RPC request.

```
wpbrute-rpc --url=https://target.example.com/xmlrpc.php --user=admin --count=500 --list=passwords.txt
```

Outcome: sends dictionary chunks of up to 500 attempts per `system.multicall`
POST to the endpoint and, on a hit, prints `Password found!` followed by
the recovered password. A `Parse error` during the run means the server
truncated the oversized response — rerun with a smaller `--count`.

### Ease the batch size for strict servers

Retry with a conservative number of attempts per request when large
batches fail or trip protection.

```
wpbrute-rpc --url=https://target.example.com/xmlrpc.php --user=admin --count=50 --list=passwords.txt
```

Outcome: still tests the entire `passwords.txt` list, just in multicall
batches of 50 — slower per dictionary pass but far under response-size
limits, which resolves parse errors and keeps the whole run inside a
handful of requests compared to one-attempt-per-request brute forcing.
