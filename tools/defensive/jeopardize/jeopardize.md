---
name: "jeopardize"
categories:
  - blackarch-defensive
version: "5.34f1d07"
homepage: "https://github.com/utkusen/jeopardize"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "utkusen/jeopardize"
github-stars: 154
github-pushed: "2022-12-08"
github-archived: true
github-issues: 3
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# jeopardize

> Hunts registered typosquatting domains for a brand you own and floods their phishing login forms with plausible fake credentials.

## What it's for

Jeopardize provides low-cost threat intelligence and response against phishing domains targeting a domain you own. Given a unique brand domain it generates typo/homograph combinations, discovers which are actually registered — either by whois brute-force across all TLDs (slow, no API key) or by filtering the last 24 hours of new registrations through a Zonefiles.io API key (fast, paid) — then scores each candidate on phishing risk factors: resolved IP, live web server, Cloudflare nameservers, site size, presence of a login form, certificate CA, registration age and Alexa rank. High-scoring domains with a login form can be automatically fed valid-looking username/password pairs from your own lists, polluting the attacker's harvest while the takedown is arranged. Results are written to a per-domain XML report. It works best for unique names; short or generic domains produce unusable combination lists. Upstream development stopped in 2022 and the Zonefiles.io integration requires editing the API key into the source.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Sweep all TLDs for registered typosquats of a brand

Free, keyless discovery by brute-forcing whois across every TLD.

```
python3 jeopardize.py --domain example.com --type brute
```

Outcome: generates typo combinations of example.com, whois-queries them across all TLDs, and writes the registered candidates with their risk flags and a numeric phishing_score to example.com.xml; login forms are not touched in this mode.

### Check the last day of new registrations for look-alikes

Fast daily-intelligence mode using the Zonefiles.io API (key configured in the source).

```
python3 jeopardize.py --domain example.com --type daily
```

Outcome: fetches every domain registered in the last 24 hours, matches them against the generated brand combinations, and appends hits with their analysis data to example.com.xml.

### Refresh a previously generated results file

Incremental follow-up on an earlier run's findings.

```
python3 jeopardize.py --domain example.com --type incremental
```

Outcome: re-reads example.com.xml, re-scrapes the domains already recorded there to update their scores, and runs the daily scan for new registrations in the same pass.

### Feed fake credentials to detected phishing login forms

Active response: poison the credential harvest with plausible junk (requires selenium and chromedriver).

```
python3 jeopardize.py --domain example.com --type daily -U user.txt -P pass.txt
```

Outcome: for high-scoring detected domains that expose a login form, automatically fills the form with username/password pairs drawn from the supplied lists; add -v for verbose output.
