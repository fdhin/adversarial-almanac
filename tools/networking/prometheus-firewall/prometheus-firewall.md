---
name: "prometheus-firewall"
categories:
  - blackarch-networking
version: "176.a316d66"
homepage: "https://github.com/averagesecurityguy/prometheus"
packaged-by: [blackarch]
blackarch-builddate: "2025-04-29"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "averagesecurityguy/prometheus"
github-stars: 48
github-pushed: "2015-05-01"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# prometheus-firewall

> Parses exported firewall configurations (Cisco ASA/PIX, SonicWALL, AWS EC2) and reports the weaknesses it finds.

## What it's for

Prometheus (the Ruby firewall analyzer by averagesecurityguy — unrelated to the Go monitoring system of the same name) audits firewall configuration files rather than live devices: it auto-detects the config type among Cisco (ASA/PIX), SonicWALL, and Amazon EC2 security groups, parses interfaces, management settings, and access lists, then runs rule checks for weak or risky configuration and renders an HTML report from a template. It is useful as a fast first pass over a firewall export during a configuration review. It does not enumerate or test a live firewall — an unrecognized config raises a parse error.

## Typical use cases

### Audit a firewall configuration export

Get a vulnerability-focused report from a config dump.

```
prometheus-firewall -c asa-config.txt
```

Outcome: detects the config type, runs the vendor checks, and writes an HTML report into the current directory (default template) summarizing identified vulnerabilities, interfaces, management settings, and access lists.

### Write the report to a fixed path with verbose progress

Keep dated reports and watch the analysis live.

```
prometheus-firewall -c sonic-config.txt -r fw-report.html -v
```

Outcome: writes the report to `fw-report.html` instead of the default name and prints verbose parsing/analysis output to the console (`-d` for even more debug detail).

### Render findings through a custom template

Match the report layout to your own documentation.

```
prometheus-firewall -c ec2-security-groups.txt -t my-template.html
```

Outcome: generates the report by substituting the supported tags in your template (--name--, --type--, --summary_statement--, --analysis--, --interfaces--, --management--, --access_lists--), giving full control over presentation.

*No corpus evidence — derived from upstream documentation.*
