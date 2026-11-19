---
name: "spike-proxy"
categories:
  - blackarch-webapp
version: "148"
homepage: "http://www.immunitysec.com/resources-freesoftware.shtml"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# spike-proxy

> Immunity's open Python web proxy for finding application-level vulnerabilities (SPIKE family).

## What it's for

SPIKE Proxy is Immunity Security's free web-application auditing proxy, part of the SPIKE
protocol-fuzzing family. It sits between the tester's browser and the target application and
looks for application-level weaknesses — covering the basics such as SQL injection and
cross-site scripting — while its open Python infrastructure allows advanced users to script
custom analysis. It runs on Linux (Python plus pyOpenSSL) and Windows, and dates from the
mid-2000s. The packaged BlackArch command `spike-proxy` is a wrapper that executes
`python2 spkproxy.py` from `/usr/share/spike-proxy`.

This is a historical tool: the original Immunity download page is no longer live, and its
documented usage (proxy configuration, listening ports, and the interactive analysis flow)
is no longer available from an authoritative source. No verified command examples can be
given — launch the wrapper and consult the `.txt` documentation shipped under
`/usr/share/doc/spike-proxy/` for the tool's operating instructions.

No corpus evidence — derived from upstream documentation (archived Immunity page and packaging).
