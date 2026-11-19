---
name: "bridgekeeper"
categories:
  - blackarch-recon
version: "57.55c390c"
homepage: "https://github.com/0xZDH/BridgeKeeper"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "0xZDH/BridgeKeeper"
github-stars: 131
github-pushed: "2022-11-19"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# bridgekeeper

> Harvest employee names from LinkedIn via search engines and convert them into username and email candidates.

## What it's for

BridgeKeeper automates the first step of many internal engagements: turning a company name into a list of its employees and their probable usernames. It scrapes search-engine results (Google, DuckDuckGo and Yahoo; Bing is disabled in the current version) for LinkedIn profiles tied to the target company, parses out person names — stripping titles, certifications and prefixes — and converts each name to one or more username formats such as `{f}{last}` or `{f}{last}@example.com`, including middle names, hyphenated surnames and character-limited forms. With a Hunter.io API key it can also identify a domain's email pattern and pull known addresses. It does not validate credentials: the output is a candidate list to feed into follow-on tooling.

## Typical use cases

### Harvest employee names and build usernames

Search for a company's staff and convert every parsed name to a username format.

```
bridgekeeper --company "Example Ltd." --format {f}{last} --depth 10 --output example-employees
```

Outcome: searches each engine up to 10 pages deep for LinkedIn profiles mentioning "Example Ltd.", then writes the parsed names and their `{f}{last}` usernames (e.g. `jsmith`) to files in the example-employees output directory.

### Enrich with Hunter.io email patterns

Identify the target's email format and scrape known addresses alongside the name harvest.

```
bridgekeeper --company "Example Ltd." --domain example.com --api API_KEY --output example-employees
```

Outcome: additionally queries Hunter.io for the email format used by example.com and its known addresses, so the output contains full email candidates (e.g. `jsmith@example.com`) next to the scraped names.

### Convert an existing name list

Skip scraping and only reformat names you already collected.

```
bridgekeeper --names names.txt --format {f}{last}[4]@example.com --output example-employees
```

Outcome: each `First (M) Last` line in names.txt is converted to the given format — here first initial plus the first four letters of the surname at example.com — with no search-engine traffic generated.

*No corpus evidence — derived from upstream documentation.*
