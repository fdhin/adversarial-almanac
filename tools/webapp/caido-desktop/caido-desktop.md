---
name: "caido-desktop"
categories:
  - blackarch-webapp
version: "0.57.1"
homepage: "https://caido.io/"
packaged-by: [blackarch, aur, nixpkgs]
blackarch-builddate: "2026-07-12"
build-system: other
license: custom:private
meta-checked: "2026-11-09"
github-repo: "caido/caido"
worth: 7.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-stars: 2723
github-pushed: "2026-10-30"
github-archived: false
github-issues: 742
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 14
gh-activity-checked: "2026-11-09"
---

# caido-desktop

> Desktop application build of the Caido intercepting proxy.

## What it's for

This package ships the desktop (AppImage) build of Caido, the web-application auditing proxy — the same tool as [[caido-cli]], packaged as a GUI application instead of the standalone CLI. Internally the desktop app runs the Caido CLI server as a background process and presents the GUI in a local webview window; the full CLI reference, matching/replay/scan workflows and headless deployment patterns are documented on the [[caido-cli]] note. The desktop build adds centralized management for multiple instances, pre-configured browser launch profiles, and offline usability — everything else (intercepting proxy, match/replace, replay, automation) is identical between the two.

## Typical use cases

### Launch the Caido desktop app

Start auditing from a native window.

```
caido
```

Outcome: starts the bundled Caido instance as a background process and opens the GUI in a webview window; the same GUI remains reachable from a browser, and the instance accepts the usual CLI options for listeners and data paths.

No corpus evidence — derived from upstream documentation (docs.caido.io, CLI vs Desktop concept page).
