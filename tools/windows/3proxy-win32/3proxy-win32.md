---
name: "3proxy-win32"
categories:
  - blackarch-windows
version: "0.9.6.1"
homepage: "http://3proxy.ru/"
packaged-by: [blackarch]
blackarch-builddate: "2026-06-29"
build-system: windows
license: Apache-2.0, GPL-2.0-or-later
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# 3proxy-win32

> The Windows build of [[3proxy]] — a single `3proxy.exe` that turns a Windows host into an HTTP/SOCKS proxy or port forwarder using the same plain-text config as the Linux version.

## What it's for

Running 3proxy where only a native Windows binary will do: a Windows host that needs to become a pivot without installing anything. Features, config syntax and behavior match [[3proxy]] (HTTP proxy, SOCKS4/5, port mapping, ACLs, users); the difference is packaging — one standalone executable plus a config file, no installer. The config examples in the [[3proxy]] note apply here verbatim.

## Typical use cases

### Start the proxy from a config file

```
3proxy.exe 3proxy.cfg
```

Outcome: the services declared in the config start immediately — with `proxy -p3128` and `socks -p1080` in the file, an HTTP proxy and a SOCKS5 proxy listen on those ports until the process is stopped.

### Run it as a persistent Windows service

```
3proxy.exe 3proxy.cfg --install
```

Outcome: 3proxy is registered as a Windows service with that config and survives reboots. Manage it with `net stop 3proxy` / `net start 3proxy`; remove the registration with `3proxy.exe --remove` after stopping the service.

*No corpus evidence — derived from upstream documentation.*
