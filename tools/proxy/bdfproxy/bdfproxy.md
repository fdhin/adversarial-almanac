---
name: "bdfproxy"
categories:
  - blackarch-proxy
version: "107.276c367"
homepage: "https://github.com/secretsquirrel/BDFProxy"
packaged-by: [blackarch]
blackarch-builddate: "2021-08-01"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "secretsquirrel/BDFProxy"
github-stars: 1019
github-pushed: "2021-07-31"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bdfproxy

> Backdoor Factory as a MITM proxy: patch executables with an embedded payload while they are downloaded over HTTP.

## What it's for

BDFProxy (secretsquirrel) glues the Backdoor Factory onto mitmproxy so that executables downloaded over plain HTTP through the proxy are transparently backdoored before reaching the client: the payload is patched into the binary in transit (code cave or new section), the PE certificate-table pointer is zeroed so the stripped signature does not mismatch, and the delivered file runs your shellcode alongside its original behavior. The intended scenario is an authorized MITM position (rogue AP, ARP redirection, a planted host) against sites that still distribute software over unencrypted HTTP. Everything is driven by `bdfproxy.cfg` — C2 host and per-payload ports, patch method, per-platform profiles — and on start it writes a Metasploit resource file matching the C2 settings it embedded. It is legacy software: it requires mitmproxy 0.11–0.17 and cannot touch TLS-protected downloads.

## Typical use cases

### Configure C2 and launch the proxy

Set your C2 host and per-payload ports in `bdfproxy.cfg`, then:

```
./bdf_proxy.py
```

Outcome: an intercepting proxy on port 8080 (default). Executables matching your profiles are patched as they download, and a Metasploit resource script is written so `msfconsole -r` brings up the handlers for every C2 profile you configured. Do not overlap C2 ports between payloads; settings outside the `[Overall]` section can be changed on the fly without restarting.

### Test the patch path from a browser

In `bdfproxy.cfg`:

```
transparentProxy = None
```

Then point the browser at the proxy (e.g. FoxyProxy → localhost:8080) and download a test executable.

Outcome: requests flow through BDFProxy as an explicit proxy with no iptables redirection, and the downloaded executable arrives patched — a fast end-to-end check of profiles and C2 settings before wiring up transparent interception.

### Watch what is being patched

```
tail -f proxy.log
```

Outcome: streams an entry for each intercepted download — patched or blocked by your blacklist — keeping the main proxy console readable while traffic flows.

### Swap the patching strategy

In `bdfproxy.cfg`:

```
PATCH_METHOD replace
SUPPLIED_BINARY /path/to/replacement.exe
```

Outcome: matching downloads are replaced wholesale by your supplied binary instead of being patched in place (`PATCH_METHOD onionduke` binds a supplied binary to the target executable instead). Related toggles: `CODE_SIGN` for PE code signing, `RUNAS_ADMIN` to force highestAvailable in the manifest, `XP_MODE` for legacy targets.

*No corpus evidence — derived from upstream documentation.*
