---
name: "socialpwned"
categories:
  - blackarch-social
version: "v2.0.1.r7.g333d759"
homepage: "https://github.com/MrTuxx/SocialPwned"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "MrTuxx/SocialPwned"
github-stars: 1343
github-pushed: "2025-01-28"
github-archived: true
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# socialpwned

> OSINT harvester that geographically sweeps Instagram and LinkedIn (and Twitter history) for employee emails, then checks each one against leaked-credential databases.

## What it's for

SocialPwned automates one chain: collect the email addresses people have published on social networks — by location, hashtag, company or follower graph on Instagram; by company and employee list on LinkedIn; from a Twitter target's history — then optionally pass every found address to PwnDB (onion-network breach lookup), GHunt (Google-account intelligence) and Dehashed (breach API) to see what credentials and account data trail behind it. Output lands in an `output/` directory as structured files for the engagement report. It runs on your own logged-in social accounts (credentials in `credentials.json`) and its upstream APIs break routinely — the project is archived, so treat modules as best-effort.

## Typical use cases

### Dump emails published by an Instagram target and check them for breaches

```
python3 socialpwned.py --credentials credentials.json --instagram --target-ig user --pwndb
```

Outcome: sweeps the target account and its posts for exposed email addresses, printing each find and querying PwnDB for matching leaked credentials; results are written under `output/`. Add `--followers-ig --followings-ig` to extend the sweep to the account's graph.

### Enumerate a company's staff on LinkedIn and pivot to breaches

```
python3 socialpwned.py --credentials credentials.json --linkedin --search-companies "Example Corp" --employees --pwndb
```

Outcome: resolves the company by name, harvests its employee list and their public emails, then breach-checks each; `--company <id>` targets a known LinkedIn company ID directly, and `--add-contacts` grows your own network's visibility instead of breach-checking.

### Enrich a single email with Google and breach data

```
python3 socialpwned.py --credentials credentials.json --ghunt --email-gh user@example.com --dehashed --email-dh user@example.com
```

Outcome: GHunt reports what the Google account behind that address exposes (profile, photos, calendar, ...) and Dehashed returns any breach records its API holds — the enrichment step after any email discovery run.

*No corpus evidence — derived from upstream documentation.*
