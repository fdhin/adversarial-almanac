---
name: "darktortilla-rat-telegram-exfiltration-payload-extraction-analysis"
categories:
  - backdoor
version: "git (2026-09-05)"
homepage: "https://github.com/kaandemir993/darktortilla-rat-telegram-exfiltration-payload-extraction-analysis"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: scripts
meta-checked: "2026-11-09"
github-repo: "kaandemir993/darktortilla-rat-telegram-exfiltration-payload-extraction-analysis"
github-stars: 49
github-pushed: "2026-09-05"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
worth: 4.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 0
---

# darktortilla-rat-telegram-exfiltration-payload-extraction-analysis

> Reverse-engineering write-up of the DarkTortilla RAT: card/browser data theft and Telegram, SMTP and FTP exfiltration — a report, not a runnable tool.

## What it's for

This repository is a malware-analysis publication, not software you install or run. Its artifact is the README itself: a staged reverse-engineering walkthrough of a DarkTortilla RAT sample (obtained from MalwareBazaar, hash linked in the write-up) performed with Process Hacker, Binary Ninja and x64dbg, with screenshots under `images/`. It documents how the RAT harvests credit-card data (10+ card brands) by querying browser `Login Data` SQLite databases (`sqlite3_open`/`sqlite3_prepare_v2`/`sqlite3_step`), decrypts saved passwords via `app_bound_encrypted_key`/`os_crypt` browser keys across 20+ browsers, stages stolen credentials in `C:\Users\Public\strEncodedData.txt`, and exfiltrates through three redundant channels — a Telegram bot API (`api.telegram.org/bot` with bot token and chat ID), SMTP via CDO.Message, and FTP. It closes with detection guidance: block the observed `showip.net` C2 domain, alert on Telegram bot-token patterns, suspicious use of SQLite APIs, and access to browser encryption keys. Use it as a defender/researcher reference for DarkTortilla behaviour and indicators; there is no CLI, no build and nothing to execute here beyond reading.

*No corpus evidence — derived from upstream documentation. Description-only note: repository contains an analysis write-up, not a runnable tool.*
