---
name: "intelplot"
categories:
  - blackarch-recon
version: "12.4dd9fc0"
homepage: "https://github.com/itsmehacker/IntelPlot"
packaged-by: [blackarch]
blackarch-builddate: "2020-04-24"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "itsmehacker/IntelPlot"
github-stars: 17
github-pushed: "2023-07-10"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# intelplot

> Self-hosted PHP web app that plots OSINT coordinates onto an offline map from KML or JSON uploads.

## What it's for

IntelPlot (itsmehacker) is a small PHP application for marking points of interest on an offline map: upload a KML or JSON file of coordinates, or search and pin a latitude/longitude manually, and the markers render locally in the browser. It is aimed at OSINT workflows where geolocated findings must be visualized without sending them to a third-party mapping service. There is no CLI beyond launching PHP's built-in web server — everything else happens in the web UI, so it needs nothing more than PHP.

## Typical use cases

### Serve the mapping UI locally

Bring up the app and open it in a browser.

```
intelplot 8080
```

Outcome: starts a PHP server bound to 127.0.0.1:8080 serving the application (the packaged wrapper is a thin shell around `php -S 127.0.0.1:<PORT>`); browse to http://127.0.0.1:8080, then upload a KML/JSON point file or enter coordinates manually to plot markers on the offline map.

*No corpus evidence — derived from upstream documentation.*
