---
name: "abuse-ssl-bypass-waf"
categories:
  - blackarch-webapp
version: "7.c28f98e"
homepage: "https://github.com/LandGrey/abuse-ssl-bypass-waf"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "LandGrey/abuse-ssl-bypass-waf"
github-stars: 318
github-pushed: "2021-07-27"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# abuse-ssl-bypass-waf

> Finds the SSL/TLS cipher suites a WAF cannot decrypt but the origin server still accepts.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Some WAFs terminate or inspect TLS with a narrower cipher list than the web server behind them: if you force a handshake with a cipher only the origin speaks, the WAF ends up passing ciphertext it cannot inspect and the payload reaches the server unfiltered. This tool automates the search: it enumerates the target's accepted ciphers with `sslscan`, then re-sends a probe request once per cipher using `curl --cipher`, checking each time whether the WAF block page appeared. It drives `curl` and `sslscan` as external commands (paths configured in `config.py`, which on non-Windows hosts must point at your real `/usr/bin/curl` and `sslscan`); it only finds the cipher — reproducing the bypassed request with that cipher is up to you. The default probe payload and the "normal" request path are defined in `config.py` and are meant to be edited for the target.

## Typical use cases

### Find a bypass cipher by matching the WAF block page

```
abuse-ssl-bypass-waf -regex 'blocked by firewall' -target https://target.example.com
```

Outcome: for every cipher the server accepts, the payload request is retried with that single cipher; each response is checked against the regex (a string or regex that appears on the WAF block page). Output is one `[-] Cipher: ... Filter By Waf!` line per cipher that still gets blocked, and on the first cipher whose response does not match it prints `[+] Success! Find Bypass Cipher:` and exits with a ready-to-run curl command line using that cipher — paste it to reproduce the unfiltered request.

### Compare response lengths when no block-page signature exists

```
abuse-ssl-bypass-waf -thread 4 -target https://target.example.com
```

Outcome: without `-regex` the tool falls back to length comparison — it records the baseline response length, then prints `[+] Cipher: ... Response Length: [N]` for each cipher whose payload response differs from the baseline (ciphers showing the same length as the blocked baseline are marked and counted). Review the differing lengths manually: a cipher that returns the origin server's real response (not the WAF page) is the bypass candidate. `-thread 4` checks ciphers in parallel; skip it on twitchy WAFs, since parallel odd-cipher handshakes are what gets the connection dropped.
