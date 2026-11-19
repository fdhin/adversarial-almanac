---
name: "spoofy"
categories:
  - blackarch-spoof
version: "v1.0.3.r10.g8e6982e"
homepage: "https://github.com/MattKeeley/Spoofy"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-28"
build-system: scripts
license: CC0-1.0
meta-checked: "2026-11-09"
github-repo: "MattKeeley/Spoofy"
github-stars: 772
github-pushed: "2026-09-27"
github-archived: false
github-issues: 2
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.5
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 15
gh-activity-checked: "2026-11-09"
---

# spoofy

> Bulk email-spoofability checker — evaluates each domain's SPF and DMARC records against a real-world-tested lookup table and reports whether sender spoofing will land.

## What it's for

Answers the pre-engagement question "which of these domains can I spoof?" with tested results rather than spec guesswork: for every domain it collects SPF (RFC 7208, includes walked) and DMARC records (including the org-domain tree walk and `sp`/`np` subdomain tags), then maps the combination onto a spoofability table that was empirically validated against live mail providers — output is a code 0-9 verdict (spoofable, subdomain-spoofable, maybe/quarantine-dependent, not spoofable, or DNS lookup failed). Lookups run over a shared caching resolver with failover, and DKIM selector enumeration is available as an option. Output goes to stdout, an Excel workbook, or JSON. It does not send any mail — it only predicts deliverability of a spoofed sender.

## Typical use cases

### Check a single domain

```bash
spoofy -d example.com
```

Outcome: prints the domain's SPF and DMARC records and a verdict code with `SPOOFING_POSSIBLE` true/false/null (null = "might be possible", e.g. `p=quarantine` with `pct` < 100, or mailbox-dependent outcomes).

### Assess a whole client estate to a spreadsheet

```bash
spoofy -iL domains.txt -o xls --dkim -t 10
```

Outcome: reads one domain per line (blank lines and `#` comments skipped), runs ten threads, enumerates DKIM selectors via the API for each, and writes an Excel workbook with the per-domain verdicts — a sortable deliverable for the report. `-o json` emits the same data for tooling.

### Resolve via an internal DNS view

```bash
spoofy -d example.com --dns-server 198.51.100.10
```

Outcome: SPF/DMARC collection runs against the specified resolver instead of the default public ones (1.1.1.1, 8.8.8.8, 9.9.9.9), which is what you want when the records differ on an internal view or the engagement network egress is restricted.

*No corpus evidence — derived from upstream documentation.*
