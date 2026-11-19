---
name: "phishingkithunter"
categories:
  - blackarch-social
version: "20.ac9bd1e"
homepage: "https://github.com/t4d/PhishingKitHunter"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: AGPL3
meta-checked: "2026-11-09"
github-repo: "t4d/PhishingKitHunter"
github-stars: 242
github-pushed: "2019-01-21"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.7
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# phishingkithunter

> Find phishing kits which use your brand/organization's files and image'.

## What it's for

PhishingKitHunter (PKHunter) mines YOUR OWN web server access logs to find
deployed phishing kits that impersonate your brand. When a phishing kit
copies your site's CSS, JS or images, the victim's browser sends referers
pointing at the kit's URL back to your legitimate servers. The tool parses
log files for those non-legitimate referers (matched against regexes you
define in its config), then checks each discovered kit URL: whether it is
still live, hashes its page for tracking, and enriches the domain with
WHOIS data. It is defensive monitoring of brand abuse — the input is your
log file, not the internet at large.

## Typical use cases

### Hunt phishing kits in a web server log

Run the core detection pass over one log file.

```
./PhishingKitHunter.py -i access-2017.log
```

Outcome: Prints every phishing-kit URL found from referer analysis with
its timestamp, live/dead HTTP status and page hash, and writes a CSV
report (default name `PKHunter-report-<date>-<time>.csv`) suitable for
takedown requests.

### Use a custom detection config and report name

Tailor referer regexes to the brand and control the output file.

```
./PhishingKitHunter.py -i access-2017.log -o PKHunter-report.csv -c conf/test.conf
```

Outcome: Matches referers against the regexes in the given config file
instead of `conf/defaults.conf`, and writes the CSV report to the
specified path.

*No corpus evidence — derived from upstream documentation.*
