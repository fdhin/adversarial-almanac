---
name: "facebrok"
categories:
  - blackarch-social
version: "33.0f6fe8d"
homepage: "https://github.com/PowerScript/facebrok"
packaged-by: [blackarch]
blackarch-builddate: "2023-07-07"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "PowerScript/facebrok"
github-stars: 58
github-pushed: "2017-04-07"
github-archived: true
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# facebrok

> PHP/MySQL phishing platform (2014–2017) that ran credential-harvesting campaigns against Facebook accounts from pretense templates managed in a web panel.

*No corpus evidence — derived from upstream documentation.*

## What it's for

A server-side social-engineering toolkit from the era before Facebook locked down its platform: upload the PHP files to a host with PHP and MySQL, complete the built-in web installer, then drive campaigns from the admin panel. Attacks are built from "templates" — pretense pages such as a fake login, a video prompt, a crashed-browser message or a profile view — each generating a link to send to the target; submitted credentials and target details (including geolocation) land in the panel, and a "Spammer" sub-platform helped distribute links, with automatic translation across languages. Brute-force functionality was dropped in the final v1.9. As a phishing architecture it is a self-hosted, template-based page cloner in the class of [[zphisher]] — capturing what the victim submits — not a reverse-proxy MitM framework like [[evilginx2]], which harvests live session cookies; for a phishing framework with a real CLI see those notes. The repository has been archived since 2017 and its templates target long-changed Facebook pages, so it survives as a historical example of phishing-platform design. It ships no command-line interface — all interaction is through the web installer and admin panel.
