---
name: "instagramosint"
categories:
  - blackarch-social
version: "21.30b5735"
homepage: "https://github.com/sc1341/InstagramOSINT/"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "sc1341/InstagramOSINT"
github-stars: 1700
github-pushed: "2024-06-08"
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

# instagramosint

> Scrape profile attributes and public photos from an Instagram account.

## What it's for

InstagramOSINT (repository archived upstream) pulls the profile attributes you cannot get from the app UI alone: follower/following/post counts, bio, profile-picture URL, business-account and verification flags, business category, Facebook linkage, external URL, joined-recently and privacy status, plus the account's public photos. It runs as a one-shot CLI or as an importable Python class for embedding in a larger collection script. Instagram routinely breaks scrapers of this kind and the project is no longer maintained — treat results as best-effort and verify anything load-bearing by hand.

## Typical use cases

### Pull a profile from the command line

```
python3 main.py --username target
```

Outcome: creates a directory for the profile and writes the scraped attributes as a dict/JSON dump inside a text file there (username, counts, bio, flags, picture URL), and downloads the account's public photos.

### Embed the scraper as a module

```
python3 -c 'from InstagramOSINT import InstagramOSINT
ig = InstagramOSINT(username="target")
ig.print_profile_data()
ig.save_data()
ig.scrape_posts()'
```

Outcome: the same collection available as a Python object — `ig.profile_data` (also indexable, e.g. `ig["Username"]`), console dump, saved data file, and scraped posts — for use inside a custom application.

*No corpus evidence — derived from upstream documentation.*
