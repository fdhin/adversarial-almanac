---
name: "blackeye"
categories:
  - blackarch-social
version: "v2.5.r8.gbdc11a2"
homepage: "https://github.com/Git-Ankitraj/blackeye-im"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-05"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "Git-Ankitraj/blackeye-im"
github-stars: 628
github-pushed: "2023-04-11"
github-archived: false
github-issues: 80
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# blackeye

> Menu-driven phishing-page kit that serves clones of 40 popular login pages and exposes them through ngrok, serveo or localtunnel tunnels.

## What it's for

A Bash script for quickly standing up credential-harvesting pages in an authorized lab or awareness-training exercise. You pick a template from a numbered menu (Instagram, Facebook, Google, PayPal, Steam, Wi-Fi session, ...), pick a tunnelling method, and the script serves the cloned login page locally while forwarding it to a public URL, with optional link shortening. Submitted credentials are stored per template, and each visitor's IP and User-Agent are logged and enriched with country/region/city lookups. It only captures web form credentials — no payload delivery or non-web interaction.

## Typical use cases

### Launch a cloned login page behind a tunnel

```
blackeye
```

Outcome: prints the numbered template menu (01 Instagram ... 40 Custom). After you pick a template and a port-forwarding method (ngrok/serveo/localtunnel), the page is served locally and the public tunnel URL is displayed; choosing 40 (Custom) instead prompts for your own titles, field labels and submit caption. Hand the URL to the consented test participant.

### Review captured credentials and visitor metadata

After a submission lands, inspect the per-template capture files.

```
cat sites/instagram/saved.usernames.txt
cat sites/instagram/ip.txt
```

Outcome: `saved.usernames.txt` accumulates Account/Pass pairs (each fresh submission first lands in `usernames.txt`), and `ip.txt` holds the visitor's IP and User-Agent which the script enriches with geolocation lookups when displaying results.

*No corpus evidence — derived from upstream documentation.*
