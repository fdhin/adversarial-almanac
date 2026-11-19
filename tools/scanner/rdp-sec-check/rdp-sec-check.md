---
name: "rdp-sec-check"
categories:
  - blackarch-scanner
version: "11.d0cc143"
homepage: "https://github.com/portcullislabs/rdp-sec-check"
packaged-by: [blackarch]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL2
meta-checked: "2026-11-09"
github-repo: "portcullislabs/rdp-sec-check"
github-stars: 268
github-pushed: "2024-03-29"
github-archived: false
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# rdp-sec-check

> Perl script that enumerates the security settings of an RDP (Terminal Services) endpoint without authenticating.

## What it's for

Determines how an RDP service on TCP 3389 is protected: which security layers
are supported (Standard RDP, TLS, CredSSP/NLA), which native encryption
strengths Standard RDP allows (40-bit, 56-bit, 128-bit, FIPS), and flags known
weaknesses — Standard-RDP-only services (MITM-prone), weak encryption enabled,
and missing Network Level Authentication (DoS exposure). It needs no
credentials, only network connectivity to the target port. It does not attempt
any login or exploitation; it reads what the negotiation handshake advertises.
For cipher-strength-only checks of native RDP encryption, see the companion
note [[rdp-cipher-checker]] (the F-Secure script of the same era).

## Typical use cases

### Check a single RDP service

Enumerate protocol and encryption support for one host.

```
rdp-sec-check 198.51.100.10
```

Outcome: prints per-check lines (`Checking if RDP/TLS/CredSSP Security is
supported...`, then each `ENCRYPTION_METHOD_*` probe) and finishes with a
summary of protocol support, encryption level, and any issues found, e.g.
`ONLY_RDP_SUPPORTED_MITM`, `WEAK_RDP_ENCRYPTION_SUPPORTED`,
`NLA_NOT_SUPPORTED_DOS` — the issue names map directly to hardening actions
(enforce NLA/CredSSP, require TLS or FIPS).

### Sweep a target list and keep a log

Run the same enumeration over many hosts and capture everything to a file.

```
rdp-sec-check --file targets.txt --outfile rdp-results.txt
```

Outcome: each host in `targets.txt` is checked in turn and the complete output
is teed into `rdp-results.txt` (`--file` takes the host list, one per line;
`--outfile` mirrors all stdout to the log), giving an auditable record of the
sweep.

### Tune for slow or lossy links

Raise timeouts and retries when targets are behind filtering or high latency.

```
rdp-sec-check --timeout 30 --retries 4 198.51.100.10
```

Outcome: receive timeouts per connection attempt extend to 30 seconds and
failed connections are retried 4 times before the host is reported unreachable
(defaults are 10 seconds and 2 retries); add `--verbose` for fuller progress
output.

*No corpus evidence — derived from upstream documentation (Portcullis Labs
rdp-sec-check README and the packaged rdp-sec-check.pl GetOptions block).*
