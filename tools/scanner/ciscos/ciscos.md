---
name: "ciscos"
categories:
  - blackarch-scanner
version: "1.3"
homepage: ""
packaged-by: [blackarch]
blackarch-builddate: "2017-01-14"
build-system: other
license: custom:unknown
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# ciscos

> Fast classful scanner for Cisco routers still carrying the factory telnet password.

## What it's for

ciscos (Cisco Scanner v1.3, by Ravi_C) sweeps a class A, B or C address range for Cisco devices with telnet open and the default password "cisco" still in place. For each responding host it connects on telnet, recognizes the router's "User Access Verification" password prompt and tries the default password, storing confirmed devices in cisco.txt in the current directory. The scan fans out forked children, tunable with thread-count and timeout flags. It only checks the telnet login password — no enable-secret testing, config retrieval or non-Cisco telnet fingerprinting.

No corpus evidence — derived from the upstream source.

## Typical use cases

### Sweep a class C range

Check one /24 for default-password routers.

```
ciscos 198.51.100 3
```

Outcome: every host in 198.51.100.0/24 is telnet-probed; routers that answer with the default password are appended to cisco.txt in the working directory.

### Sweep a class B range

Cover a /16 the same way.

```
ciscos 198.51 2
```

Outcome: identical default-password check across the whole class B; a class A sweep works the same with a single octet and class code 1 (`ciscos 198 1`).

### Tune concurrency and timeout

Control scan speed and per-connect patience.

```
ciscos -C 300 -t 4 198.51.100 3
```

Outcome: up to 300 parallel probe processes run with a 4-second per-connection timeout (the tool's header recommends 300 threads or fewer and 3-5 second timeouts).
