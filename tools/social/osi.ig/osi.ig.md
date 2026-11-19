---
name: "osi.ig"
categories:
  - blackarch-social
version: "101.4debaa2"
homepage: "https://github.com/th3unkn0n/osi.ig"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "th3unkn0n/osi.ig"
github-stars: 1589
github-pushed: "2024-02-01"
github-archived: false
github-issues: 52
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# osi.ig

> Instagram OSINT Tool gets a range of information from an Instagram account.

## What it's for

OSI.IG (Open Source Information — Instagram) pulls information about an
Instagram account that is not visible on the public profile page: user id,
follower/following counts, upload counts, profile image URL, business
category, external URL, the target's most-used hashtags and mentioned
accounts, any email exposed in profile data, and per-post details such as
captions, location, timestamp and image URLs. After Instagram stopped
serving anonymous JSON responses, the tool requires an Instagram login:
credentials are supplied when prompted, and the target account is queried
through that session. It queries one account at a time by username and
does not perform bulk enumeration or scraping of followers.

## Typical use cases

### Profile overview of a target account

Recon one Instagram username.

```
python3 main.py -u target
```

Outcome: After an interactive Instagram login, prints the profile block
(id, followers/following, uploads, business enum, external URL), tags and
mentions summary, and any email found in the account data.

### Include post-level details

Dig into the target's uploads rather than just the profile.

```
python3 main.py -u target -p
```

Outcome: Adds per-post information — accessibility caption, location,
timestamp, caption and picture URL — for the target's recent posts
(sensitive-content posts may be skipped).

*No corpus evidence — derived from upstream documentation.*
