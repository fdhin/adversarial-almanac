---
name: "zgrab2"
categories:
  - blackarch-fingerprint
version: "v1.0.0.r93.g835aa53"
homepage: "https://github.com/zmap/zgrab2"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-10-25"
build-system: go
license: APACHE, ISC, custom:unknown
meta-checked: "2026-11-09"
github-repo: "zmap/zgrab2"
github-stars: 2211
github-pushed: "2026-10-22"
github-archived: false
github-issues: 45
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 106
gh-activity-checked: "2026-11-09"
---

# zgrab2

> The ZMap project's fast, modular application-layer scanner: pipes target lists into per-protocol grabber modules (http, ssh, tls, jarm, banner, smtp, mongodb, …) and emits a JSON record — including full handshake transcripts — for each host.

## What it's for

In-depth follow-up scanning at the scale ZMap finds hosts: ZMap answers "which IPs have port X open", zgrab2 completes the application-layer handshake and records what the service actually said. Each protocol is a module invoked as a subcommand (`zgrab2 http`, `zgrab2 ssh`, `zgrab2 telnet`, `zgrab2 jarm`, plus banner, TLS, SMTP, FTP, POP3, IMAP, NTP, Redis, MongoDB, MySQL, PostgreSQL, MSSQL, SMB, Modbus, DNP3, BACnet, MQTT, AMQP, Oracle, IPP, Fox, PPTP, SOCKS5, ManageSieve, Memcached and Siemens). Unlike most scanners it stores detailed transcripts of the exchange — every message of a TLS handshake, the full certificate chain, SSH host keys — as one JSON object per target for offline analysis. Targets arrive on stdin as CSV lines (`IP, DOMAIN, TAG, PORT`, any subset), so it composes with zmap output, shuf, or plain echo; CIDR blocks are expanded automatically. It only performs unauthenticated handshakes — no exploits, no credential guessing — which keeps Internet-wide surveying defensible. For fingerprinting work, the tls and jarm modules plus banner grabs across ports are the standard tools.

## Typical use cases

### Grab a website over HTTP

Fetch a specific path and record the server's response.

```
echo example.com | zgrab2 http --max-redirects=1 --endpoint=/
```

Outcome: connects to port 80, performs the HTTP request, follows at most one redirect, and prints one JSON line with status, headers and body details, followed by a scan-complete summary line (targets/sec, success rate).

### Capture an SSH banner and host keys

Record what an SSH endpoint presents.

```
echo 198.51.100.10 | zgrab2 ssh
```

Outcome: completes the SSH handshake to port 22 and prints a JSON record containing the server version banner, key-exchange algorithms and host key data; `-p 22`-style port overrides apply to every module.

### Raw banner grab on an arbitrary port

See whatever bytes an unknown service sends first.

```
echo 198.51.100.10 | zgrab2 banner -p 443
```

Outcome: connects to the given port, optionally sends the module's probe string, and records up to `--max-read-size` KiB of whatever the service returns — the quickest way to identify an unknown listener; `--probe` changes the bytes sent.

### Active TLS fingerprint with JARM

Compute a JARM hash for a TLS endpoint.

```
echo example.com | zgrab2 jarm -p 443
```

Outcome: sends the ten JARM probe handshakes to the target and emits the JARM fingerprint string in the JSON result — a single hash that characterises the server's TLS stack for comparison against known-good or known-bad values.

### Run several protocols in one pass

Drive multiple module invocations from one INI configuration.

```
zgrab2 multiple -c multiple.ini
```

Outcome: with an INI whose `[Application Options]` section sets input/output files and further sections name modules (e.g. `[http] port=80`, `[ssh] port=22`, repeatable with unique `name=`), each target is scanned by the configured modules in one run; per-module `trigger="tag"` values select which input tags (third CSV field) each module scans, and `zgrab2 <module> -h` documents every module flag.

*No corpus evidence — derived from upstream documentation.*
