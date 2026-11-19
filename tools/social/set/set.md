---
name: "set"
categories:
  - blackarch-social
version: "8.0.3"
homepage: "https://github.com/trustedsec/social-engineer-toolkit/tags"
packaged-by: [blackarch, kali]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "trustedsec/social-engineer-toolkit"
github-stars: 15443
github-pushed: "2026-06-04"
github-archived: false
github-issues: 26
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 39
gh-activity-checked: "2026-11-09"
---

# set

> TrustedSec's Social-Engineer Toolkit: a menu-driven framework that chains spear-phishing mail, cloned websites and credential harvesters for authorised social-engineering assessments.

## What it's for

SET (launched as `setoolkit`) is the standard open-source toolkit for consented social-engineering tests: it generates malicious file-format payloads and emails them through its mass mailer, clones real login pages, serves credential-harvester sites, and builds infectious media, QR-code and PowerShell attack vectors — each through numbered console menus rather than long flag lists. Typical workflow: pick `Spear-Phishing Attack Vectors` to mail a payload, or `Website Attack Vectors` → `Credential Harvester Attack Method` → `Site Cloner` to clone a login page and capture whatever the target types into it. It is a Linux testing tool for engagements with explicit permission; spoofed sender addresses require a local sendmail and the `SENDMAIL=OFF`→`ON` switch in the SET config. Companion entry points `seupdate` (git update) and `seautomate` (menu replay) ship alongside the main launcher.

## Typical use cases

### Launch the interactive attack menu

```
sudo setoolkit
```

Outcome: prints the disclaimer (accept with `y`), then the main menu — Spear-Phishing Attack Vectors, Website Attack Vectors, Infectious Media Generator, Create a Payload and Listener, Mass Mailer Attack, Arduino-Based Attack Vector, Wireless Access Point Attack Vector, QRCode Generator Attack Vector, Powershell Attack Vectors, Third Party Modules. Every attack chain is driven from numbered choices beneath these entries.

### Run a spear-phishing mass-mailer campaign

Choose `1` (Spear-Phishing Attack Vectors) then `1` (Perform a Mass Email Attack).

Outcome: SET walks you through payload or template selection, then a one-off or mass mailing — it prompts for the target email list, the sending address and name, and a subject; messages are relayed through the configured SMTP path and each send is reported inline. A FileFormat Payload submenu option lets you attach an SET-generated malicious document instead of a plain template.

### Clone a site and harvest credentials

Choose `2` (Website Attack Vectors) → `3` (Credential Harvester Attack Method) → `2` (Site Cloner), then answer the URL prompt with your in-scope target.

```
Enter the url to clone: https://target.example.com/login
```

Outcome: SET clones the page and serves it locally (POST URLs rewritten to itself); when a test participant submits the form, the posted username/password fields are echoed to the console and appended to SET's harvest report under `src/program_junk/` (path per config), together with the visitor's IP and User-Agent.

### Enable sender-address spoofing for the mailer

```
sudo sed -i 's/^SENDMAIL=OFF/SENDMAIL=ON/' /etc/setoolkit/set.config
```

Outcome: with sendmail installed and this flag on, the mass mailer and spear-phishing vectors send through local sendmail so the envelope reflects your chosen spoofed From address; the setting only accepts `ON` or `OFF`.

*No corpus evidence — derived from upstream documentation.*
