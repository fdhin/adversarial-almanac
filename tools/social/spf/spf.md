---
name: "spf"
categories:
  - blackarch-social
version: "85.344ac2f"
homepage: "https://github.com/tatanus/SPF"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "tatanus/SPF"
github-stars: 450
github-pushed: "2019-08-08"
github-archived: false
github-issues: 18
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# spf

> SpeedPhish Framework: one command that recon's a domain (emails, subdomains), spins up phishing sites and can mail the harvested targets — the demo-lab tool from DEF CON 23.

## What it's for

SPF (SpeedPhish Framework, by Adam Compton / tatanus) compresses a whole social-engineering exercise into a single invocation: given a target domain it discovers email addresses and subdomains (dnspython + PhantomJS scraping), generates lookalike phishing pages for the discovered services, stands up the web server to host them (twisted), and can send the phishing emails to the gathered targets — with a `--simulate` mode that runs the entire pipeline without actually sending. Use it for rapid, consented phishing simulations and awareness demos; note it is a 2019-frozen Python tool whose scraped sources and PhantomJS dependency age quickly.

## Typical use cases

### Dry-run the full pipeline against a domain

```
python3 spf.py --test -d example.com
```

Outcome: `--test` enables every module — email gathering (`-g`), site generation (`-w`), external tool use (`-e`) — except actual sending, with verbosity and auto-yes on; you get the discovered emails, generated site configs and a preview of what a real run would do, with no mail leaving the host.

### Run recon and build phishing sites without sending mail

```
python3 spf.py -d example.com -c "Example Corp" --ip 198.51.100.10 -g -w --simulate -y
```

Outcome: gathers target emails for example.com, generates phishing sites referenced to your web server at 198.51.100.10, and stages the emails in simulation mode instead of sending; `-y` skips the confirmation prompts so the run proceeds unattended. Swap `--simulate` for `-s` only when the engagement actually authorises sending.

### Serve the generated phishing sites on their own

```
python3 web.py default.cfg
```

Outcome: starts the twisted web server hosting the sites described in the generated config (`default.cfg` after a run) — the standalone hosting step when you want to serve pages without re-running recon, or to keep them up after spf.py exits (`-W` in a full run does the same).

*No corpus evidence — derived from upstream documentation.*
