---
name: "socialfish"
categories:
  - blackarch-social
version: "266.3589ef3"
homepage: "https://github.com/UndeadSec/SocialFish"
packaged-by: [blackarch]
blackarch-builddate: "2026-06-01"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "UndeadSec/SocialFish"
github-stars: 4935
github-pushed: "2026-05-20"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 16
gh-activity-checked: "2026-11-09"
---

# socialfish

> UndeadSec's phishing framework: a web panel that clones modern login pages with a real browser, captures credentials, cookies and even live 2FA codes, and exposes the lure through ngrok or cloudflared.

## What it's for

SocialFish v3 turns phishing-kit setup into a panel workflow: you give it a target login URL, it clones the page with Playwright (handling JS-heavy and multi-step logins like Office 365), serves the clone at an unguessable lure URL tunneled through ngrok or cloudflared, and shows a live operator view of everything a victim submits — credentials, full cookie jars with security attributes, and OTP codes, which the operator can relay back into the victim's session in real time. Sessions, victim IPs/geolocation/device data and network traffic are logged and exportable to JSON/CSV. It is a dual-use red-team/awareness tool: point it only at systems you are authorised to test.

## Typical use cases

### Launch the operator panel

```
python SocialFish.py admin Password1
```

Outcome: starts the server; browse to `http://localhost:5000/neptune` and log in with the credentials given on the command line to reach the templates, tunneling and sessions views.

### Clone a login page and expose it through a tunnel

In the panel: `/templates` → New Template → paste the in-scope login URL, pick a clone mode (`both` credentials+cookies, `login` credentials only, `cookies` session only), then click "Tunnel" and choose ngrok or cloudflared, and "Lure" to copy the generated URL.

Outcome: the cloned page goes live at the tunnelled lure URL; the template is saved for reuse across engagements.

### Monitor captures in real time

In the panel: `/sessions` (credentials, cookies, victim IP/geolocation/device, full request log) and `/admin/otp_panel.html` for 2FA.

Outcome: each victim interaction streams into the operator view; OTP codes arriving on the interception panel can be injected back to the victim's browser to complete a 2FA login, and the whole session exports to JSON or CSV for the report.

*No corpus evidence — derived from upstream documentation.*
