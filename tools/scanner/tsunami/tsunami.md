---
name: "tsunami"
categories:
  - scanner
homepage: "https://github.com/google/tsunami-security-scanner"
github-repo: "google/tsunami-security-scanner"
github-match: "exact"
origin: "awesome-pentest"
recipe: "upstream"
license: Apache-2.0
github-stars: 8620
github-pushed: "2026-11-13"
github-commits-year: 49
gh-checked: "2026-11-14"
worth: 7.8
worth-basis: "full (activity+popularity+recency+adoption)"
packaged-by: []
---

# tsunami

> Google's extensible network security scanner — plugin-driven detection of high-severity, high-confidence vulnerabilities across network ranges.

## What it's for

Tsunami is a general-purpose network security scanner from Google built around an extensible plugin system: a Java core engine orchestrates detector plugins (Java and Python, plus templated ones) that flag only high-severity exposures — exposed admin consoles, unauthenticated RCE services, credentialed-but-default services — and it deliberately biases toward high confidence over coverage, to keep the finding stream actionable. It ships with a callback server (for out-of-band interaction detection) and a Python plugin server, and is distributed as Docker images rather than a standalone binary. Its positioning is defensive: it exists so enterprises can sweep their own (usually cloud) ranges for the exposures an attacker would find first — but that same output is exactly the initial triage an attacker runs, so the tool reads both ways. All publicly available plugins live in the separate `google/tsunami-security-scanner-plugins` repository.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Get the ready-to-run scanner image

```
docker pull ghcr.io/google/tsunami-scanner-full
docker run -it --rm ghcr.io/google/tsunami-scanner-full bash
```

Outcome: a shell inside the full image — core engine, every plugin category and the callback server under `/usr/tsunami` — which is the only supported way to run Tsunami without compiling anything yourself.

### Scan a host for high-severity exposures

```
tsunami --ip-v4-target=198.51.100.10
```

Outcome: the engine fingerprints open ports and runs every bundled detector against the responsive services, printing each finding with the detector that produced it.

### Enable Python-written detectors

```
tsunami-py-server > /tmp/py_server.log 2>&1 &
tsunami --ip-v4-target=198.51.100.10 --python-plugin-server-address=127.0.0.1 --python-plugin-server-port=34567
```

Outcome: starts the Python plugin server, then runs the scan with Python-based detectors included; omit the `--python-` arguments entirely if you did not start the server.

### Narrow a scan to one port or one detector

```
tsunami --ip-v4-target=198.51.100.10 --port-ranges-target=443 --detectors-include="detector-name"
```

Outcome: scans only the given port range and runs only the named detector (the name defined in the plugin's `PluginInfo` / `info.name` section) — the fast way to re-check a single known exposure.

### Run the out-of-band callback server

```
tsunami-tcs > /tmp/tcs_server.log 2>&1 &
```

Outcome: starts Tsunami's callback server, which detectors use to confirm blind SSRF/RCE-style interactions; when running it inside Docker, forward its listening port with `docker run -p`.
