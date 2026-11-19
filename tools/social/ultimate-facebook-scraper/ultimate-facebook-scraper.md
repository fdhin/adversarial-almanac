---
name: "ultimate-facebook-scraper"
categories:
  - blackarch-social
version: "236.5661bdc"
homepage: "https://github.com/harismuneer/Ultimate-Facebook-Scraper"
packaged-by: [blackarch]
blackarch-builddate: "2020-12-07"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "harismuneer/Ultimate-Facebook-Scraper"
github-stars: 3183
github-pushed: "2025-06-07"
github-archived: false
github-issues: 20
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# ultimate-facebook-scraper

> Logged-in Selenium bot that harvests a Facebook profile's photos, videos, friends and public timeline posts into organized folders.

## What it's for

Ultimate Facebook Scraper (Haris Muneer and Hassaan Elahi) automates a logged-in browser session — Chrome driven by Selenium, authenticated with your own Facebook account (it cannot use an account with 2FA enabled) — to scrape what a profile exposes to that account: uploaded and tagged photos, videos, the friends list with per-friend profile pictures (followers, following, work/college friends), all public posts on the timeline, and posts inside groups. Targets are listed in `input.txt` (one profile or group link per line) and credentials go in `credentials.yaml`; it deliberately avoids the Graph API, so there are no app rate limits but also no access beyond what the logged-in account can see. Its structural weakness is that extraction relies on hard-coded XPath selectors, so every Facebook DOM change breaks scrapes until selectors are patched — with the last real maintenance years ago and the project README replaced by the author's profile page, expect to repair selectors before a successful run today.

## Typical use cases

### Scrape the profiles listed in input.txt

```
ufb-scraper
```

Outcome: reads `credentials.yaml` and `input.txt`, opens Chrome with your session, and for every listed profile writes downloaded photos, videos, the friends list with profile pictures and all public timeline posts into an organized output tree; run `python scraper.py` from the repo if not using the packaged launcher.

### Tune a photo-heavy scrape

```
ufb-scraper --uploaded_photos True --friends_photos True --total_scrolls 500 --scroll_time 8
```

Outcome: limits timeline scrolling to 500 iterations at 8 seconds per scroll (the defaults are 2500/8) — a bounded first run; `--friends_small_size True` / `--photos_small_size True` fetch thumbnails instead of full-size images for much faster passes over large friend lists.

### Harvest posts from a Facebook group

```
ufb-scraper --uploaded_photos False --friends_photos False
```

Outcome: with photo downloads disabled and group links in `input.txt`, the run focuses on collecting the group's posts — group scraping needs the group link in the same one-per-line input format as profiles.

*No corpus evidence — derived from upstream documentation.*
