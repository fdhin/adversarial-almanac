---
name: "sslcat"
gentoo-category: "net-analyzer"
version: "1.0"
homepage: "http://www.bindshell.net/tools/sslcat"
origin: "pentoo"
recipe: "pentoo-ebuild"
packaged-by: [pentoo]
build-system: make
license: BSD
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# sslcat

> netcat for SSL: pipes stdin into an SSLv2/v3/TLS connection and writes the decrypted response to stdout.

## What it's for

SSLCat (Dave Armstrong, bindshell.net, v1.0) is a simple Unix utility that reads and writes data across an SSL-enabled network connection. Given a hostname and optional port (443 by default) it attempts an SSLv2 connection — `-3` forces SSLv3, `-T` TLSv1 — then sends stdin across the encrypted channel and prints what comes back on stdout, making it the pipeline-friendly way to speak HTTP or any line protocol to an SSL endpoint. It is deliberately bare: no cipher enumeration or certificate reporting (use a TLS scanner for those), just the encrypted pipe. The original bindshell.net site is offline; this reference is derived from the archived tool page.

## Typical use cases

### Fetch server headers over SSL

```
echo 'HEAD / HTTP/1.0\r\n\r\n' | sslcat www.example.com 443 | grep ^Server
```

Outcome: performs the SSL handshake, sends the HEAD request through it and prints the filtered `Server` header — a quick fingerprint of an SSL endpoint using only standard Unix tools around it.

### Force a specific protocol version

```
echo 'HEAD / HTTP/1.0\r\n\r\n' | sslcat -3 www.example.com
echo 'HEAD / HTTP/1.0\r\n\r\n' | sslcat -T www.example.com
```

Outcome: repeats the probe with SSLv3 (`-3`) or TLSv1 (`-T`); a server that only accepts one protocol answers only under that flag, so the pair doubles as a minimal protocol-support check.

### Interact with a TLS service manually

```
sslcat target.example.com 443
```

Outcome: opens the SSL connection with your terminal attached — type protocol lines directly, as with netcat, and watch the responses; `-d` drops the connection when stdin hits EOF instead of waiting.

*No corpus evidence — derived from upstream documentation (archived bindshell.net tool page).*
