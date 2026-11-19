---
name: "social-mapper"
categories:
  - blackarch-social
version: "190.92be8da"
homepage: "https://github.com/SpiderLabs/social_mapper"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "SpiderLabs/social_mapper"
github-stars: 4092
github-pushed: "2022-02-25"
github-archived: false
github-issues: 48
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# social-mapper

> Correlates a company's employees to their social-media profiles at scale using facial recognition, producing a reviewable HTML report per platform.

## What it's for

Social Mapper (Jacob Wilkin / SpiderLabs) takes a target organisation's staff — harvested from LinkedIn by company name, supplied as a folder of named photos, or as a CSV of names and image URLs — then searches Facebook, Twitter, Instagram, LinkedIn, Pinterest, VKontakte, Weibo and Douban for matching names and compares candidate profile pictures against your input images with facial recognition to cut false positives. The output is a per-platform report a human reviews quickly, optionally with generated email lists for phishing platforms like Gophish. It drives real browsers (Firefox + Selenium + geckodriver) with social-media credentials you paste into `social_mapper.py`, is no longer actively maintained, and site-side detection limits heavy use (disposable accounts for Facebook in particular).

## Typical use cases

### Harvest a company's staff from LinkedIn and match them across all platforms

```
python3 social_mapper.py -f name -i "Example Corp" -m accurate -a
```

Outcome: logs into LinkedIn, pulls employee names and profile photos for Example Corp, then searches every supported platform and facially matches candidates; an HTML report per site lands in the output directory showing photo pairs and profile links for human review. `-m fast` skips a target after the first likely match (up to ~20x quicker, more misses).

### Match a folder of photos across chosen platforms

```
python3 social_mapper.py -f imagefolder -i ./targets -m accurate -fb -tw -ig
```

Outcome: each image in `./targets` (filename = person's name) is searched on Facebook, Twitter and Instagram only; matches above the recognition threshold appear in per-site reports. Tune precision with `-t loose|standard|strict|superstrict` when reviewing shows too many false positives or misses.

### Generate per-platform email lists for a phishing tool

```
python3 social_mapper.py -f csv -i targets.csv -m fast -li -e "<f><last>@example.com"
```

Outcome: for every confirmed LinkedIn match, emits a CSV with firstname, lastname, fullname, generated email, profileURL and photoURL — the exact columns a mail-merge into Gophish or similar consumes.

*No corpus evidence — derived from upstream documentation.*
