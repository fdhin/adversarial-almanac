---
name: "tftp-proxy"
categories:
  - blackarch-proxy
version: "0.1"
homepage: "http://www.c0decafe.de/"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# tftp-proxy

> Python 2 TFTP interception proxy that answers requests from a local cache, fetching misses from an upstream TFTP server and rewriting them through loadable modules.

## What it's for

tftp-proxy (Daniel Mende, c0decafe.de) answers TFTP requests on port 69 like an ordinary server, but any file not already in its root directory is downloaded from a designated upstream TFTP server, optionally passed through loadable Python modules that can modify or re-sign it, cached, and then served to the requester. The point is to sit inline where clients fetch firmware or configuration over TFTP and transparently tamper with what they receive while remaining a drop-in TFTP endpoint — the shipped `ctl` module demonstrates patching `CTL` control files and updating `.sgn` signature files on the fly, and doubles as the template for custom modules. It is a small Python-2-era utility (2012) with no source repository; the author's tarball is the only upstream. Needs root to bind UDP 69.

## Typical use cases

### Front an upstream TFTP server transparently

Act as the TFTP server clients talk to, backed by the real one.

```
sudo tftp-proxy /tmp/tftproot 198.51.100.10
```

Outcome: listens on UDP 69; the first request for a file prints `*** Asked for <file>, fetching... ***`, downloads it from the upstream server at 198.51.100.10 into /tmp/tftproot, and serves it — subsequent requests for the same file are answered from the local cache.

### Rewrite fetched files on the fly with a module

Load a tamper module that touches each file before it is served.

```
sudo tftp-proxy -m ctl /tmp/tftproot 198.51.100.10
```

Outcome: the `ctl` module from the packaged modules directory is imported at startup; requested files whose names start with `CTL` get patched (`### Is a CTLFile, modifying... ###`) and `.sgn` files have their signature updated before serving. Modules export a `run(filename, path)` hook returning the path to serve, which is the interface to copy for custom rewriting.

### Debug requests and upstream transfers

Turn on verbose protocol logging.

```
sudo tftp-proxy -d /tmp/tftproot 198.51.100.10
```

Outcome: the bundled tftpy library switches from error-only to debug logging, printing each read request, block transfer and upstream fetch as it happens — useful to confirm which files clients actually pull through the proxy.

*No corpus evidence — derived from upstream documentation.*
