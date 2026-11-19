---
name: "artlas"
categories:
  - blackarch-defensive
version: "154.e5fdd8d"
homepage: "https://github.com/mthbernardes/ARTLAS"
packaged-by: [blackarch]
blackarch-builddate: "2026-02-14"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "mthbernardes/ARTLAS"
github-stars: 126
github-pushed: "2023-05-22"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# artlas

> Real-time Apache/Nginx access-log analyzer that flags OWASP-pattern attacks and pushes alerts to Telegram, Zabbix, or syslog/SIEM.

## What it's for

ARTLAS tails web-server access logs in real time, matches requests against a PHP-IDS-derived OWASP Top 10 filter set (`etc/default_filter.json`), and raises alerts when exploitation attempts are detected — delivered to a Telegram group or user, to Zabbix triggers, or to a syslog/SIEM server in CEF format. It understands Apache, Nginx, and their vhost log layouts via a format mask, so one instance can watch several sites. It watches HTTP request patterns only — it is not a WAF and does not block anything; it tells your incident channel that someone is probing.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Bind it to your access log and format

Point the analyzer at the right file and parsing mask before first run.

```
grep -E 'apache_log|apache_mask|vhost_enable' etc/artlas.conf
```

Outcome: shows the `[General]` configuration keys that decide which file is tailed and how each line is split into fields; set `apache_log` to the full path of the access log, `apache_mask` to its field layout, and `vhost_enable` if the logs are per-vhost.

### Run the analyzer under screen

Start continuous monitoring that survives a disconnected shell.

```
screen -S artlas
python artlas.py
```

Outcome: ARTLAS begins tailing the configured access log and classifying every request through the OWASP filter set; detected attempts are reported and delivered to each enabled channel (`[Telegram]`, `[Zabbix]`, `[CEF_Syslog]`). Detach with Ctrl+A D and reattach later with `screen -r artlas` to review the live stream.
