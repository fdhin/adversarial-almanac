---
name: "gocabrito"
categories:
  - blackarch-social
version: "4.33ac59a"
homepage: "https://github.com/KINGSABRI/goCabrito"
packaged-by: [blackarch]
blackarch-builddate: "2025-04-29"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "KINGSABRI/goCabrito"
github-stars: 55
github-pushed: "2021-11-28"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gocabrito

> Profile-driven Ruby sender for targeted phishing campaigns over SMTP.

## What it's for

goCabrito is a command-line mailer for phishing engagements: it authenticates to an SMTP account you control and delivers an HTML/text message to a single address, a plain list, or a CSV of named recipients (`fname,lname,email`, no header), paced in groups with configurable delays between batches. Messages support personalization and tracking tags — `{{name}}` filled from the CSV, `{{track-open}}` as a tracking pixel, `{{track-click}}` appended to a URL, `{{num}}` as a random phone number — plus attachments and reusable JSON profiles so each campaign's settings live in one file. It is a sender only: it does not host phishing pages or collect results itself; the companion getCabrito server can import the per-recipient hash database it optionally writes.

## Typical use cases

### Dry-run a campaign profile

Validate the profile and message rendering without delivering anything.

```
goCabrito.rb -P campaigns/example/profile.json --dry
```

Outcome: processes the profile exactly as a real run — SMTP settings, body tags, recipient lists — but sends no mail; catches wrong credentials, missing files, or broken body tags before the launch.

### Launch a paced campaign with attachments

Send an HTML body to a named-recipient CSV, throttled in groups with a delay between batches.

```
goCabrito.rb -s smtp.example.com:587 -u user@example.com -p 'Password1' \
  -f user@example.com -t targets.csv \
  -B message.html -S "Quarterly review" -a report.docx \
  -g 10 -d 60
```

Outcome: authenticates to the SMTP server and delivers the message to every address in targets.csv in batches of 10 with 60-second pauses between batches, attaching report.docx to each mail.

### Send a personalized, tracked campaign

```
goCabrito.rb -P campaigns/example/profile.json -D campaign.sqlite
```

Outcome: each recipient's mail is personalized (`Dear {{name}},` becomes their first name), every `{{track-open}}`/`{{track-click}}` tag resolves to a unique per-recipient hash, and address/hash pairs are written to the campaign.sqlite database for import by the getCabrito tracking server.

*No corpus evidence — derived from upstream documentation.*
