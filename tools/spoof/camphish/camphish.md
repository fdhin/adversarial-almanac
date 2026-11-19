---
name: "camphish"
categories:
  - spoof
version: "git (2025-04-29)"
homepage: "https://github.com/techchipnet/camphish"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "techchipnet/camphish"
github-stars: 5263
github-pushed: "2025-04-29"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
worth: 4.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 240
---

# camphish

> TechChip's CamPhish: a bash script that hosts a templated page on a built-in PHP server, exposes it through ngrok or a Cloudflare tunnel, and captures photos (and optionally GPS coordinates) from a target's front camera after they grant the page camera permission.

*No corpus evidence — derived from upstream documentation.*

## What it's for

A consent-trick harvester rather than an exploit: the target opens the link, the browser asks for camera permission, and if the target allows it the page quietly grabs shots from the phone's front camera or webcam. Engagement templates make the permission prompt feel natural — "Festival Wishing" (a greeting-card generator), "Live YouTube TV" (a fake video player) and an "Online Meeting" page — and version 2.0 added GPS location capture with a Google Maps view of the results. Requires `php` and `wget`; the tunnel (ngrok or Cloudflare) exists only so the link works over the internet. It is fully interactive — there are no command-line flags beyond starting the menu — and `cleanup.sh` wipes captured images and location data afterwards. Pair the captured output with the awareness that this tests exactly one thing: whether users grant camera/location permission to an unknown page.

## Typical use cases

### Run a camera-capture campaign

One sentence of intent: generate a tunnelled link that photographs anyone who grants camera access.

```
cd CamPhish && bash camphish.sh
```

Outcome: presents a menu to pick a tunnel (ngrok or Cloudflare) and a template, then starts the PHP server and prints a public URL. Forward it to the target; when they open it and allow camera access, captured shots accumulate locally and are listed by the script. Choose a template matching the pretext for the highest engagement rate.

### Wipe captured material

One sentence of intent: remove harvested images, locations and logs between engagements.

```
bash cleanup.sh
```

Outcome: deletes the captured cam files and saved GPS locations along with generated junk and logs, restoring a clean checkout for the next run.
