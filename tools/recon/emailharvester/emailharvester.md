---
name: "emailharvester"
categories:
  - recon
version: "1.3.2+git20191005-0parrot1"
homepage: "https://github.com/maldevel/EmailHarvester"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: python
meta-checked: "2026-11-09"
github-repo: "maldevel/EmailHarvester"
github-stars: 994
github-pushed: "2026-06-07"
github-archived: false
github-issues: 17
github-match: "exact"
gh-checked: "2026-11-07"
worth: 5.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 4
gh-activity-checked: "2026-11-09"
---

# emailharvester

> Harvest email addresses for a domain by scraping search engines and site-specific searches, with txt/xml export.

## What it's for

EmailHarvester (maldevel) retrieves email addresses belonging to a domain by querying popular search engines (Google, Bing, Yahoo, ASK, Baidu, Dogpile, Exalead) and, through its plugin system, site-targeted searches on platforms like Twitter, LinkedIn, GitHub, Instagram, Reddit and YouTube. It was inspired by theHarvester but focuses on domain email collection. Reach for it in the OSINT phase of an engagement to build a username/wordlist seed or a phishing target list; results can be limited, proxied, saved to txt and xml, and printed or silenced. It does not verify deliverability or enumerate a mail server directly — it only aggregates what search engines have indexed.

## Typical use cases

### Harvest from a single search engine

Collect addresses for a domain using one engine.

```
EmailHarvester.py -d example.com -e google
```

Outcome: prints every `@example.com` address the selected engine surfaces for the domain; swap `google` for `bing`, `yahoo`, `ask`, `baidu`, `dogpile` or `exalead`.

### Harvest from a specific site via a search engine

Use the engine plugins to focus on one platform.

```
EmailHarvester.py -d example.com -e linkedin
```

Outcome: returns addresses associated with the domain that appear on the chosen platform (also `twitter`, `github`, `instagram`, `reddit`, `youtube`) — typically employee addresses.

### Harvest from everything and exclude noisy sources

Run all engines and plugins at once, minus the ones you don't want.

```
EmailHarvester.py -d example.com -e all -r twitter,ask
```

Outcome: aggregates results from every source except the excluded plugins and prints the deduplicated address set.

### Limit volume and export to file

Cap the crawl and write results out.

```
EmailHarvester.py -d example.com -e all -l 200 -s emails.txt
```

Outcome: limits processing to 200 results per source and writes the harvested addresses to `emails.txt` plus `emails.xml` for downstream tooling. Add `-x http://127.0.0.1:8080` to route queries through a proxy, or `-u "MyUserAgentString 1.0"` for a custom User-Agent.

No corpus evidence — derived from upstream documentation.
