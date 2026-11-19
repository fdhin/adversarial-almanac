---
name: "whatsapp-osint"
categories:
  - recon
version: "git (2025-10-16)"
homepage: "https://github.com/kinghacker0/whatsapp-osint"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: python
meta-checked: "2026-11-09"
github-repo: "kinghacker0/whatsapp-osint"
github-stars: 1296
github-pushed: "2025-10-16"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
worth: 4.1
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 27
---

# whatsapp-osint

> Interactive menu tool that looks up WhatsApp profile data by phone number — photo, about text, business flag, linked devices, privacy settings — via a RapidAPI endpoint.

## What it's for

WHAT'S OSINT (kinghacker0) wraps the "WhatsApp OSINT" API on RapidAPI in a colored terminal menu: enter a phone number with country code and pick one of six lookups — profile photo (auto-saved as JPG), about/status text, business-account verification, linked devices, privacy settings, or a full combined pull. It does not log into WhatsApp or automate the app; it is a thin client over a commercial API, so its reach is exactly what that service can see on a given number (hidden photos or privacy-restricted profiles simply report as not available). It needs a RapidAPI subscription key placed in a `.env` file (`RAPIDAPI_KEY=...`, copied from `.env.example`) before first run. Use only in authorized investigations — querying persons without consent or legal basis is misuse regardless of what the API permits.

## Typical use cases

### Run an interactive profile lookup

Check what a WhatsApp account exposes publicly.

```
python3 whatsapp-osint.py
```

Outcome: opens the numbered menu (options 1–6 for about, base64 photo, business, devices, doublecheck, privacy); enter a number in international format (e.g. `51987654321`), and the tool validates the format, calls the API for the selected query, prints the returned fields to the terminal and saves any profile photo as a JPG file in the working directory.

*No corpus evidence — derived from upstream documentation.*
