---
name: "nipper-ng"
categories:
  - identify
version: "0.11.10-1kali3"
homepage: ""
origin: "kali"
recipe: "kali-debian"
packaged-by: [kali]
build-system: other
aliases: ["nipper"]
meta-checked: "2026-11-09"
github-repo: "arpitn30/nipper-ng"
github-stars: 79
github-pushed: "2022-06-28"
github-archived: false
github-issues: 1
github-match: "search"
gh-checked: "2026-11-07"
worth: 2.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# nipper-ng

> Network-device configuration auditor: parses a saved router/firewall/switch config and writes a security review report with findings and recommendations.

## What it's for

Nipper ("Network Infrastructure Configuration Parser", originally by Ian Ventura-Whiting "Fizz" of Titania, formerly CiscoParse) takes the text configuration of a network device — typically the captured output of `show run` — and produces a structured security review: inventory of the configuration plus observations about weak services, default credentials, SNMP communities, filtering rules, and management-plane exposure, each with recommendations. `nipper-ng` is the free GPL fork of the last open-source release, 0.11.10, packaged by Kali (command `nipper`); Titania's later Nipper became the commercial Nipper Studio/enterprise product, so the `-ng` fork freezes the 2008-era engine — device support and checks do not reflect current firmware. You must tell it the device type; it does not auto-detect. Supported types include Cisco IOS router/switch/catalyst (`--ios-router` default, `--ios-switch`, `--ios-catalyst`), Cisco PIX/ASA/FWSM firewalls, CatOS and NMP catalysts, Cisco CSS, Juniper ScreenOS (`--screenos`), Nortel Passport (`--passport`), SonicWall SonicOS (`--sonicos`), and Check Point Firewall-1 (`--fw1`).

## Typical use cases

### Audit a Cisco IOS router configuration

Turn a saved `show run` capture into a report.

```
nipper --ios-router --input=ios.conf --output=report.html
```

Outcome: reads `ios.conf`, parses it device-specifically, and writes `report.html` — an HTML audit listing the parsed configuration plus each security issue found with a recommendation (HTML is the default output format).

### Produce an XML report for tooling

Get machine-readable findings instead of prose.

```
nipper --asa --input=asa.conf --xml --output=audit.xml
```

Outcome: audits the Cisco ASA configuration and emits XML (`--html` default, `--text` and `--latex` also available), suitable for feeding into a report pipeline or diffing between audits.

### Audit a non-Cisco firewall

Run the same review against a Juniper ScreenOS device.

```
nipper --screenos --input=netscreen.conf --output=report.html
```

Outcome: parses the ScreenOS configuration and reports the same class of findings for that platform; for Check Point Firewall-1, pass the `conf` directory as `--input`. Without an explicit device flag the tool assumes `--ios-router`, so always state the type.

*No corpus evidence — derived from upstream documentation (Quickstart.txt and Options.txt from the packaged nipper-ng 0.11.10 source).*
