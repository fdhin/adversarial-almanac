---
name: "datajackproxy"
categories:
  - blackarch-proxy
version: "42.f75f3a3"
homepage: "https://github.com/nccgroup/DatajackProxy"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "nccgroup/DatajackProxy"
github-stars: 103
github-pushed: "2019-08-22"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# datajackproxy

> Tap a native application's TLS buffers by injecting into the process — plaintext of non-HTTP, certificate-pinned protocols without a network MITM.

## What it's for

DatajackProxy (NCC Group) targets traffic no HTTP proxy can see: native applications speaking non-HTTP or certificate-pinned protocols. Instead of intercepting the network, it injects JavaScript into the running process via the Frida API and hooks the TLS library itself — OpenSSL SSL_Read/SSL_Write on Linux, Schannel EncryptMessage on Windows — reading data before outbound encryption and after inbound decryption, and on Linux modifying it there too. Use it to analyze or tamper with a desktop client's custom binary protocol when a certificate-authority MITM is not an option. Platform support: Linux does full read/write of both directions; on Windows only reading outbound plaintext is implemented. Requires Python 3 and the Frida bindings.

## Typical use cases

### Attach to a process by PID

```
python DatajackProxy.py -p 1234
```

Outcome: Frida injects into PID 1234, hooks its SSL_Read/SSL_Write calls, and prints the process's plaintext TLS traffic — with the ability to modify buffers — as the application communicates.

### Attach by process name

```
python DatajackProxy.py -n thunderbird
```

Outcome: same instrumentation as attaching by PID, with the target process resolved by name.

### Attach to a Windows process

```
python DatajackProxy.py -n OUTLOOK.EXE -o windows
```

Outcome: hooks the Schannel path (EncryptMessage) of the named Windows process and displays outbound plaintext prior to encryption; Windows write support is not implemented.

*No corpus evidence — derived from upstream documentation.*
