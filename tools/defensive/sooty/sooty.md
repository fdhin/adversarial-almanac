---
name: "sooty"
categories:
  - blackarch-defensive
version: "335.6236fd1"
homepage: "https://github.com/TheresAFewConors/Sooty"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "TheresAFewConors/Sooty"
github-stars: 1494
github-pushed: "2024-09-25"
github-archived: false
github-issues: 19
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# sooty

> Interactive SOC-analyst console that bundles the repetitive triage steps — URL sanitizing, link decoders, reputation lookups, hashing and phishing-mail analysis — behind numbered menus.

## What it's for

Sooty automates the routine checks a SOC analyst repeats on every ticket, freeing time for deeper analysis. It is a menu-driven Python program, not a flag-driven CLI: launch it once and pick numbered options per task. Capabilities include sanitizing URLs for safe emailing; decoding Proofpoint-rewritten URLs, Office SafeLinks, UTF-8/Base64 strings and Cisco type-7 passwords (plus Unfurl URL expansion); reputation checks for IPs, URLs and email addresses (VirusTotal, AbuseIPDB, BadIPs, botvrij, TOR exit-node lists); DNS, reverse-DNS and WhoIs lookups; file and text hashing with optional VirusTotal hash comparison; and phishing-email analysis (extracting URLs, sender addresses, headers and IPs, HaveIBeenPwned breach lookup, and generated response templates). API-backed features (VirusTotal, URLScan.io, AbuseIPDB, HaveIBeenPwned, PhishTank, EmailRep) need their keys in `config.yaml`; the tool runs without keys minus those features. It triages indicators and emails — it does not scan hosts or networks.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Run the analyst console

Start the interactive toolkit and work tickets from its menus.

```
sooty
```

Outcome: a numbered main menu appears (Sanitize URL's, Decoders, Reputation Checker, DNS Tools, Hashing Functions, Phishing Analysis, URL Scan). Choosing an option prompts for the input — e.g. paste a Proofpoint-rewritten URL into the ProofPoint Decoder to recover the original destination, or an IP into the Reputation Checker for a multi-source verdict; results print to the console and the menu reappears for the next task. From a source checkout the same console starts with `python Sooty.py`.
