---
name: "capfuzz"
categories:
  - blackarch-sniffer
version: "34.97ac312"
homepage: "https://github.com/MobSF/CapFuzz"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: python
license: GPL3
meta-checked: "2026-11-09"
github-repo: "MobSF/CapFuzz"
github-stars: 64
github-pushed: "2019-09-01"
github-archived: true
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# capfuzz

> MITM web proxy from the MobSF project that captures, fuzzes and intercepts HTTP(S) traffic — built for mobile-app API testing.

*No corpus evidence — derived from upstream documentation.*

## What it's for

CapFuzz (Ajin Abraham / MobSF) is a mitmproxy-based HTTPS proxy with four modes — `capture` records a client's flows, `intercept` lets you tamper with them live through a Python hook, `fuzz` serves a web UI to configure fuzzing of captured requests, and `runfuzz` fuzzes from the command line. It is tailored to mobile-app and web API testing: record an app's backend calls, then replay them with fuzzed parameters. The upstream repository is archived (2019) and its README marks the tool deprecated in favor of MobSF's `httptools`; it remains usable as packaged, but expect Python-3-era dependency drift and no fixes. It needs the mitmproxy CA certificate trusted on the client to see HTTPS content.

## Typical use cases

### Record an app's API traffic

```
capfuzz -m capture -n myapp -p 1337
```

Outcome: starts the HTTP(S) proxy on port 1337; point the mobile app or browser at it as proxy and trust the CA under `capfuzz/ca` — flows are recorded under the `myapp` project name for later fuzzing.

### Fuzz the captured requests from the web UI

```
capfuzz -m fuzz -n myapp -p 1337
```

Outcome: serves the fuzzer's configuration web UI on the same port; select the captured project's requests in the browser, set fuzz points and payloads, and run the fuzz jobs from the UI.

### Fuzz from the command line

```
capfuzz -m runfuzz -n myapp
```

Outcome: replays and fuzzes the `myapp` project's captured requests with the default configuration, reporting responses in the terminal — the scriptable path for pipelines.

### Tamper with flows live

```
capfuzz -m intercept -n myapp
```

Outcome: the proxy passes every request/response through `capfuzz/core/interceptor.py` (relative to the installed package) — edit that hook to rewrite headers, bodies or URLs in flight, the upstream-documented extension point for manual MITM testing.
