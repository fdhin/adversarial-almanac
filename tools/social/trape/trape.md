---
name: "trape"
categories:
  - blackarch-social
version: "132.6baae24"
homepage: "https://github.com/jofpin/trape"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "jofpin/trape"
github-stars: 9068
github-pushed: "2024-06-20"
github-archived: false
github-issues: 255
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# trape

> Serves a cloned decoy page that silently tracks every visitor's location, device and network while offering live social-engineering hooks.

## What it's for

Trape (Jose Pino, @jofpin; presented at BlackHat Asia Arsenal 2018) is an OSINT "people tracker": it clones a web page you choose, serves it on your box, and once a target visits the lure it reports their geolocation path, device and browser profile, network speed and even the devices on their LAN, while detecting which online services the target is logged into (session recognition). On top of tracking it can push real-time social-engineering "processes" to the visitor — phishing any domain, injecting JavaScript (e.g. a keylogger), or serving files — and can expose the whole panel through an ngrok tunnel. Reach for it in authorized red-team training to demonstrate how much quiet intelligence a single clicked link yields; it is not a credentials-brute-forcing or exploitation framework, and as a 2018-era tool its browser-tracking tricks are increasingly blunted by modern browser privacy controls. Dual-licensed: MIT for code, CC-BY-3.0 for README content.

## Typical use cases

### Clone a decoy page and start the tracking panel

```
trape --url http://example.com --port 8080
```

Outcome: clones example.com, serves the copy on port 8080, prints an auto-generated access key and opens the trape control panel in your browser; every visitor to the lure appears with live location and device data.

### Serve a hand-crafted local lure instead of cloning

```
trape --local lure.html --port 8080 --accesskey mypanelkey
```

Outcome: serves your own HTML file as the lure (replacing `--url`) and protects the control panel with a chosen key instead of the random one — useful when you want full control of the page the target sees.

### Publish the trap through an ngrok tunnel

```
trape --url http://example.com --port 8080 --ngrok <authtoken>
```

Outcome: trape connects to ngrok with the supplied authtoken and prints a public HTTPS URL that tunnels to your local server, so targets outside your network can reach the lure without port forwarding.

### Customize the REST API injection path

```
trape --url http://example.com --port 8080 --injectcode api.js
```

Outcome: the REST-API hook file that victim browsers load is named `api.js` instead of a random token-like name, letting you reference it predictably from other sites you control.

*No corpus evidence — derived from upstream documentation.*
