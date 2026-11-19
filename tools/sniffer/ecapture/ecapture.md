---
name: "ecapture"
categories:
  - sniffer
version: "git (2026-11-07)"
homepage: "https://github.com/gojue/ecapture"
origin: "kitploit"
recipe: "upstream"
packaged-by: [aur, nixpkgs]
build-system: go
meta-checked: "2026-11-09"
github-repo: "gojue/ecapture"
github-stars: 15530
github-pushed: "2026-11-08"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 169
gh-activity-checked: "2026-11-09"
worth: 8.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 3
---

# ecapture

> Captures SSL/TLS plaintext in the process with eBPF uprobe hooks — no MITM proxy, no CA certificate.

## What it's for

eCapture (gojue, formerly published under the ehids organization) uses eBPF to capture the plaintext of encrypted connections from inside the processes that create it. Instead of interposing a proxy or planting a trusted CA, it attaches uprobes to the crypto libraries — OpenSSL/LibreSSL/BoringSSL, GnuTLS, NSS/NSPR — and to Go programs' TLS stacks, and reports the plaintext bytes each process reads and writes, tagged with PID, command and file descriptor. The same mechanism powers audit modules that record bash/zsh command lines and MySQL/PostgreSQL queries on the host. It runs on Linux (x86_64 kernel 4.18+, aarch64 5.5+, LoongArch64 6.6+) and Android, needs root or specific capabilities, and does not support Windows or macOS. TLS output comes in three modes: live text, a keylog of TLS master secrets for Wireshark, and a pcapng with embedded decryption secrets.

## Typical use cases

### Capture TLS plaintext live

See what an OpenSSL-linked process actually sends and receives.

```
sudo ecapture tls
```

Outcome: eCapture locates the system OpenSSL library, attaches its hooks, and prints plaintext reads and writes as they happen — each event timestamped with PID/TID, command name and FD, followed by the payload (e.g. `GET / HTTP/1.1` from a curl request).

### Export TLS keys for Wireshark

Decrypt a packet capture taken independently.

```
sudo ecapture tls -m keylog --keylogfile=openssl_keylog.log
```

Outcome: TLS master secrets are appended to `openssl_keylog.log` in NSS key-log format; capture traffic with tcpdump in parallel and Wireshark (or live `tshark -o tls.keylog_file:openssl_keylog.log`) decrypts those sessions with no proxy in the path.

### Record a decryptable pcapng

Keep both packets and secrets in one file.

```
sudo ecapture tls -m pcap -i eth0 --pcapfile=ecapture.pcapng tcp port 443
```

Outcome: encrypted packets and the TLS secrets needed to decrypt them are written together to `ecapture.pcapng` (filtered by the trailing pcap expression); Wireshark opens it and decrypts the captured sessions using the embedded secrets.

### Capture TLS inside a Go binary

Hook statically-linked Go TLS rather than a shared library.

```
sudo ecapture gotls --elfpath=/path/to/go-client --hex
```

Outcome: eCapture attaches to the Go program at the given path; once the program runs, its HTTPS/TLS plaintext is reported (hex-encoded with `--hex`), giving visibility into Go services that no library-level hook can reach.

### Audit shell commands on a host

Record what operators type, from the shell process itself.

```
sudo ecapture bash
```

Outcome: bash (module `zsh` for zsh) command lines are captured as they are executed and logged with process attribution — host-security monitoring without touching shell history files, which users can edit.

### Audit database queries

Watch SQL reaching MySQL or PostgreSQL.

```
sudo ecapture mysqld
```

Outcome: SQL queries sent to MySQL 5.6/5.7/8.0 or MariaDB are captured and printed with the issuing process (module `postgres` covers PostgreSQL 10+), providing query-level auditing without server-side logging or credentials.

*No corpus evidence — derived from upstream documentation.*
