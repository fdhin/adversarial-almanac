---
name: "proxenet"
categories:
  - blackarch-webapp
version: "712.67fc6b5"
homepage: "https://github.com/hugsy/proxenet"
packaged-by: [blackarch]
blackarch-builddate: "2020-01-08"
build-system: cmake
license: GPL2
meta-checked: "2026-11-09"
github-repo: "hugsy/proxenet"
github-stars: 222
github-pushed: "2016-11-29"
github-archived: true
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# proxenet

> MITM web proxy for pentests whose request/response rewriting happens in plugins you write in Python, Ruby, Lua and more.

## What it's for

A hacker-friendly Burp-style interception proxy built in C: point a browser at it and every HTTP(S) request/response passes through hook functions you implement in a high-level language — Python, Ruby, Lua, Perl, Tcl, Java or even C — instead of a proprietary extension API. HTTPS is intercepted on the fly with per-host certificates generated from its own CA (keys live under `~/.proxenet/keys`), and matching rules decide which hostnames actually get piped through plugins. Archived upstream since 2016, so it lacks modern HTTP/2 handling — treat it as a lightweight, scriptable alternative for labs and simple engagements, succeeded in practice by the author's later work on pwntools-style tooling. Helper clients `proxenet-control-cli.py` and `proxenet-control-web.py` attach to its control server to manage plugins at runtime.

## Typical use cases

### Start the proxy with defaults

Bring up an interception proxy on the standard port.

```
proxenet
```

Outcome: listens on `localhost:8008` (20 threads), auto-loads plugins from the `autoload` directory of `~/.proxenet/plugins`, and generates on-the-fly TLS certificates for HTTPS hosts it intercepts — point your browser's proxy settings at it to start seeing traffic.

### Bind a custom address/port and plugin directory

Run a second instance or isolate a plugin set.

```
proxenet -b 127.0.0.1 -p 9001 -x /opt/proxenet-plugins
```

Outcome: the proxy binds 127.0.0.1:9001 and loads its plugins from the given directory instead of the default `~/.proxenet/plugins`, so experimental hooks stay out of your main setup.

### Intercept selectively and chain an upstream proxy

Only touch hosts you care about, through a corporate relay.

```
proxenet -m target.example.com -X upstream.example.com -P 8080
```

Outcome: `-m/--pattern` restricts interception to matching hostnames (default mode "intercept only matches"; `-I`/`-E` flip the match sense, `*` matches everything), while all traffic is forwarded through the upstream proxy given by `-X`/`-P` (`-D` selects SOCKS4 instead of HTTP) — non-matching hosts pass through untouched.

*Provenance: no corpus evidence — derived from upstream documentation (README + `src/main.c` usage()/`config.h.in` defaults, verified 2026-11).*
