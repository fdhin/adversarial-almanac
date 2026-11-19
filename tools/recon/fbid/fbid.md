---
name: "fbid"
categories:
  - blackarch-recon
version: "16.1b35eb9"
homepage: "https://github.com/guelfoweb/fbid"
packaged-by: [blackarch]
blackarch-builddate: "2017-04-07"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "guelfoweb/fbid"
github-stars: 41
github-pushed: "2017-04-07"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# fbid

> Given a Facebook photo URL or its cached CDN filename, looks up the owning profile ID, the author's name and the photo's canonical URL.

## What it's for

fbid (guelfowweb) is a small OSINT helper for Facebook photos: Facebook's CDN filenames embed a photo ID, and from that fbid reconstructs who posted the picture. Feed it a full `fbcdn.net` photo URL or just the downloaded photo's filename (e.g. `1464008_10151964855117182_1514212999_n.jpg`) and it returns the photo's canonical `facebook.com/photo.php?fbid=...` URL plus, when privacy settings allow it, the author's profile ID and real name. The privacy status is part of the answer: publicly visible photos resolve to a named profile, while friends-only photos return only the photo URL — that distinction itself leaks how visible the picture is. It is a lookup tool only; it does not log in, crawl profiles, or batch-process files.

## Typical use cases

### Resolve a photo from its Facebook CDN URL

Identify who posted a photo you found online.

```
fbid.py https://scontent-mxp1-1.xx.fbcdn.net/v/t1.0-9/photo_n.jpg
```

Outcome: prints a JSON object with the photo's privacy status, its canonical `photourl` (`facebook.com/photo.php?fbid=...`), and for public photos the author's `profileid` and `name` — the account behind the image.

### Resolve a photo from just its filename

Work from an already-downloaded file without the URL.

```
fbid.py 1464008_10151964855117182_1514212999_n.jpg
```

Outcome: parses the photo ID embedded in the CDN-style filename and returns the same JSON lookup — status, photourl and, when public, the author's profile and name — without needing the original link.

No corpus evidence — derived from upstream documentation.
