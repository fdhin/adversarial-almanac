---
name: "urldigger"
categories:
  - blackarch-webapp
version: "02c"
homepage: "https://code.google.com/archive/p/urldigger/"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-12"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "Seabreg/urldigger"
github-stars: 0
github-pushed: "2019-01-20"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# urldigger

> Python 2 OSINT script that harvests URLs from currently-hot sources (Google, Alexa, Twitter trends) and screens them for spam, phishing and malware.

## What it's for

URLDigger (Emilio Casbas, 2010) extracts URL addresses from different "hot" sources and/or detects SPAM and malicious code in them: it can pull the current hot URLs from Alexa or Google Trends, gather result URLs from a Google search term, read URLs from a file, and then screen what it finds — checking pages for common spam words, identifying phished sites, or querying URLs against Google Safe Browsing. It was conceived as an experimental incident-response/recon helper for quickly gathering "what is hot right now" and filtering it. It is very much a 2010 tool: it runs on Python 2 with a bundled Google-scraping library, and several of its sources have changed or died, so expect degraded behavior today. The original project lived on Google Code; the linked GitHub repository is a third-party mirror of the same 02c release.

## Typical use cases

### Pull the current hot URLs

See what is trending right now.

```
urldigger -H
```

Outcome: fetches and prints the current hot URLs from Alexa (20 by default); -T shows hot topics from Twitter's main page instead.

### Gather URLs for a search term

Collect the URLs behind a query.

```
urldigger -g "free casino bonus"
```

Outcome: scrapes Google for the term (about 50 results by default) and prints the result URLs; -u lifts the result cap, and -S additionally screens each result URL for common spam words.

### Screen a site for spam wording

Check one page's content.

```
urldigger -s http://target.example.com
```

Outcome: fetches the page and reports hits for the tool's common-spam-word list; -p performs the analogous check for phished-site indicators.

### Check a URL against Google Safe Browsing

Get a malicious/known-good verdict.

```
urldigger -x http://target.example.com
```

Outcome: queries the URL against the Google Safe Browsing service and reports whether it is flagged; -w bulk-checks the URLs of a Google search, and -r crawls a URL one level and checks every link found (use sparingly — the code warns you can be banned).

No corpus evidence — derived from the packaged upstream source (urldigger.py optparse help, 02c release).
