---
name: "hemingway"
categories:
  - blackarch-social
version: "8.9c70a13"
homepage: "https://github.com/ytisf/hemingway"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "ytisf/hemingway"
github-stars: 42
github-pushed: "2016-11-27"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# hemingway

> Template-configured spear-phishing sender with its own SMTP listener.

## What it's for

Hemingway (Yuval tisf Nativ, 2014) is a compact campaign helper: it spawns its own SMTP listener, then sends one personalized phishing mail per target from a spoofed sender of your choosing, automatically re-attaching images referenced by the HTML body as inline CID parts so clients render them. All configuration is by editing the bundled template files — there is no flag surface beyond `-v`/`-h` — which makes campaigns disposable and reproducible. It deliberately does not handle SMTP reputation, landing pages, tracking, or reporting; treat it as a minimal single-purpose sender, and note it is Python 2 code on modern systems.

## Typical use cases

### Run a campaign from the template config

Point `confs/example.conf` at your assets — the `[server]` block sets the built-in listener (address, port, max_connections) and the `[phish]` block sets the recipient CSV, HTML and text bodies, subject, and comma-separated attachments — then launch.

```
python2 hemingway.py
```

Outcome: starts the SMTP listener and delivers one mail per line of the addresses CSV, each line pairing a spoofed sender with a victim (`Display Name <sender@example.net>;victim@example.net`); images found in the HTML body are attached as CID references in the delivered mails.

*No corpus evidence — derived from upstream documentation.*
