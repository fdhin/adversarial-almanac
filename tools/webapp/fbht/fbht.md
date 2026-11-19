---
name: "fbht"
categories:
  - blackarch-webapp
version: "70.d75ae93"
homepage: "https://github.com/chinoogawa/fbht"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom
meta-checked: "2026-11-09"
github-repo: "chinoogawa/fbht"
github-stars: 1631
github-pushed: "2024-05-09"
github-archived: false
github-issues: 70
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# fbht

> A menu-driven Facebook automation toolkit: batch test-account operations and phishing-vector previews.

## What it's for

fbht (Facebook Hacking Tool, by chinoogawa) is an interactive Python 2 application that drives a Selenium-controlled browser against Facebook. From its numbered menus you can batch-create and delete throwaway accounts, connect the accounts stored in its local database, automate friend requests and acceptances, and preview phishing-style post vectors with configurable privacy settings; activity is logged under the installation directory. Reach for it when auditing Facebook's own anti-abuse workflows with accounts you control. It is not a password-guessing or account-takeover tool. It is Python 2 era software, unmaintained since 2024, and modern Facebook's bot defenses may break its flows.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Launch the interactive menu

Start the toolkit.

```
fbht
```

Outcome: the packaged wrapper starts the tool and prints the numbered main menu (account operations, phishing vectors and related modules); choosing an entry walks through interactive prompts for the required parameters, with results stored in the local database and logs.
