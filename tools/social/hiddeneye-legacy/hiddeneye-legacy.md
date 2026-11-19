---
name: "hiddeneye-legacy"
categories:
  - blackarch-social
version: "RC1.r226.g7a3deaf"
homepage: "https://github.com/DarkSecDevelopers/HiddenEye-Legacy"
packaged-by: [blackarch]
blackarch-builddate: "2021-12-13"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "DarkSecDevelopers/HiddenEye-Legacy"
github-status: "gone"
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
---

# hiddeneye-legacy

> All-in-one interactive phishing framework: cloned login pages, keylogging, victim intel, tunneling.

## What it's for

HiddenEye (renamed HiddenEye-Legacy in its final days; the GitHub repository has since been deleted) is a menu-driven Python 3 social-engineering framework. It serves locally hosted clones of popular login pages, exposes them through a tunnel of your choice (localhost, ngrok, Serveo, LocalXpose, Localtunnel, Openport, PageKite), stores credentials captured from each submission, and can attach a keylogger to templates and show live victim information (IP, geolocation, ISP). Captured data can additionally be mailed out over Gmail SMTP, and custom page templates drop into `Webpages/CUSTOM/`. It needs Python 3, PHP (page serving), and pyngrok, and was formally abandoned by its own developers ("bad code and a huge amount of issues"); the successor rewrite lives in a different organization. The vault's [[hiddeneye]] note points at this same upstream.

## Typical use cases

### Run the interactive attack menu

```
sudo python3 HiddenEye.py
```

Outcome: presents the numbered menu — pick a tunnel (e.g. localhost or ngrok), a credential category, and a site template; the cloned page is then served locally or on the generated tunnel URL, credentials submitted by a visitor are written to `Server/CapturedData`, and the victim's IP/location appear in the terminal.

*No corpus evidence — derived from upstream documentation (archived repository snapshot).*
