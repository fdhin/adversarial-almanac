---
name: "bettercap-ui"
categories:
  - blackarch-misc
version: "1.4.0"
homepage: "https://github.com/bettercap/ui"
packaged-by: [blackarch, aur]
blackarch-builddate: "2026-04-15"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "bettercap/ui"
github-stars: 374
github-pushed: "2025-08-19"
github-archived: false
github-issues: 10
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bettercap-ui

> The official web interface for bettercap — a static asset tree served by bettercap itself.

*No corpus evidence — derived from upstream documentation.*

## What it's for

This package ships the official web UI of bettercap, the MITM framework (vault note: [[bettercap]] under `sniffer/`). The UI is a browser dashboard over bettercap's scanning, spoofing, proxying and credential-capture modules — the easiest way to drive the tool before learning its interactive console. It is not a standalone program: the package only installs the prebuilt web files (on BlackArch into `/usr/share/bettercap/ui`), and bettercap serves them through its `api.rest` and `http.server` modules, driven by the `http-ui`/`https-ui` caplets that ship with bettercap. Use it for a GUI over a local or remote bettercap session; the interactive console and caplet scripting remain the full-featured surface. Building the UI from its Vue.js sources is a developer and installation concern and stays in `install.md`.

## Typical use cases

### Serve the UI on localhost

```
sudo bettercap -caplet http-ui
```

Outcome: bettercap starts with `api.rest` and `http.server` bound to 127.0.0.1; open http://127.0.0.1/ in a browser and log in with the credentials configured in the `http-ui.cap` caplet file.

### Serve the UI for remote access over TLS

```
sudo bettercap -caplet https-ui
```

Outcome: the same UI, bound on 0.0.0.0 behind a self-signed TLS certificate — browse `https://<host>/` from another machine, accept the certificate warning, and log in with the `https-ui.cap` credentials.

### Refresh caplets and the web UI

```
sudo bettercap -eval "caplets.update; ui.update; q"
```

Outcome: downloads the latest caplet set and web UI release, replacing the system-wide copies, then exits — run once after installing, or to pick up UI updates. Note that `caplets.update` overwrites local edits in the system caplets folder with upstream defaults.
