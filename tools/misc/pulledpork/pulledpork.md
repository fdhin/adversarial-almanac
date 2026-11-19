---
name: "pulledpork"
categories:
  - blackarch-misc
version: "397.5ccf5c5"
homepage: "https://github.com/shirkdog/pulledpork"
packaged-by: [blackarch, aur]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL2
meta-checked: "2026-11-09"
github-repo: "shirkdog/pulledpork"
github-stars: 441
github-pushed: "2021-07-07"
github-archived: false
github-issues: 42
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pulledpork

> Perl pipeline that downloads, merges, tunes and stages Snort and Suricata rulesets from one config-driven run.

## What it's for

Snort and Suricata deployments pull rules from several sources (Talos subscriber rulesets, Emerging Threats, local rules), each arriving as its own tarball with its own state — while the detector only wants a handful of consolidated rule files. PulledPork (by JJ Cummings, Michael Shirk and the PulledPork team, 2009-2021) automates that whole pipeline: it downloads rule tarballs, verifies checksums, processes shared-object rules, applies your enable/drop/disable/modify decisions in that order of precedence, writes the consolidated ruleset plus an updated sid-msg.map, and can HUP the running daemon afterwards. Nearly every option can live in `pulledpork.conf` instead of on the command line. It manages rules only — it does not configure or tune the detector itself. The BlackArch package wraps it as the `pulledpork` command running pulledpork.pl from /usr/share/pulledpork.

## Typical use cases

### Run the whole pipeline from the config file

```
pulledpork -c pulledpork.conf -T -H
```

Outcome: downloads the tarballs configured in pulledpork.conf, merges them into the configured output rule files, skips shared-object processing (-T), and sends a HUP to the snort pid listed in the conf so the new rules are loaded (-H).

### Pull one ruleset ad hoc with a command-line oinkcode

```
pulledpork -o /usr/local/etc/snort/rules/ -O <oinkcode> \
  -u http://www.snort.org/reg-rules/snortrules-snapshot-2973.tar.gz -T -H
```

Outcome: fetches that snapshot tarball using the given oinkcode, unpacks and writes the rules into the output directory, and HUPs snort — a one-off pull without editing the conf (substitute the snapshot matching your Snort version).

### Tune rules and regenerate sid-msg.map without downloading

```
pulledpork -c pulledpork.conf -i disablesid.conf -b dropsid.conf -e enablesid.conf \
  -m /usr/local/etc/snort/sid-msg.map -h /var/log/sid_changes.log -Hn
```

Outcome: applies the enable, drop and disable decisions from the three sid config files to the already-downloaded rules, writes the refreshed sid-msg.map and a change log, and HUPs snort (-H) while -n skips all downloading.

### Manage rules for a Suricata installation

```
pulledpork -c pulledpork.conf -S suricata-3.1.3
```

Outcome: processes the configured rule files for the Suricata version named in -S (use your installed version's string); ET/ETPro rulesets are supported, Talos subscriber rules for Suricata are not.

*No corpus evidence — derived from upstream documentation.*
