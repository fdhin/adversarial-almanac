---
name: "pastemonitor"
categories:
  - blackarch-recon
version: "12.bc5d326"
homepage: "https://github.com/pixelbubble/PasteMonitor"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "pixelbubble/PasteMonitor"
github-stars: 50
github-pushed: "2025-02-05"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pastemonitor

> Pastebin Scraping-API collector that archives the daily public paste stream and emails you when your keyword wordlist matches.

## What it's for

PasteMonitor (pixelbubble) watches the public paste feed on Pastebin through the official Scraping API: each run downloads the day's new public pastes (typically 1000–3000 `.txt` files) to your machine, and when any paste matches a keyword from your `wordlist.txt`, an automatic email alert is sent. It is a leak-monitoring tripwire for red teams and defenders watching for their organization's names, domains or credentials appearing in pastes. The practical constraints are the setup: Pastebin requires a PRO account with your machine's IP whitelisted in the Scraping API settings, and the alert mailer is a Gmail account with 2-step verification plus an app password, whose credentials and the alert recipient must be edited directly into `pastemonitor.py` before running. Because pastes expire quickly, the local archive is part of the design — a paste that later disappears can be recovered from your own disk via its ID.

## Typical use cases

### Collect the day's public pastes

Run the monitor to pull the current paste stream.

```
python3 pastemonitor.py
```

Outcome: the day's new public pastes download as `.txt` files to your machine, building a searchable local archive of the feed — rerun it on a schedule to keep the archive current.

### Arm keyword alerts

Define tripwire terms and let the tool email you on a hit.

```
printf 'target.example.com\nuser@target.example.com\nPassword1\n' > wordlist.txt
python3 pastemonitor.py
```

Outcome: with one keyword per line in `wordlist.txt` (domains, addresses, product or code names), any matching paste triggers an email to the configured recipient from the Gmail account whose app password is set in `pastemonitor.py`; the paste is already on disk, so you keep the evidence even after Pastebin removes it.

> No corpus evidence — derived from upstream documentation.
