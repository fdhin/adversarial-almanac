---
name: "osint-spy"
categories:
  - blackarch-recon
version: "v2.0.r11.g03dcf48"
homepage: "https://github.com/SharadKumar97/OSINT-SPY"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "SharadKumar97/OSINT-SPY"
github-stars: 1559
github-pushed: "2023-10-23"
github-archived: false
github-issues: 12
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# osint-spy

> Flag-per-module OSINT scanner for domains, emails, IPs, Bitcoin wallets and IoT devices, plus TLS cipher/Heartbleed checks and VirusTotal sample analysis.

## What it's for

OSINT-SPY (Sharad Kumar) is a Python 3 script where each command-line flag selects one lookup module and takes its target as the value: `--domain` does domain recon, `--email` and `--ip` run identity/WHOIS lookups, `--btc_address` / `--btc_block` / `--btc_date` pull Bitcoin blockchain information, `--ssl_cipher` enumerates the ciphers a web server supports and `--ssl_bleed` tests it for Heartbleed, `--device` explores exposed IoT services by type (e.g. asterisk, juniper, windows10), and `--malware` submits a file to VirusTotal for analysis. `--json` switches the output to JSON for scripting. It is aimed at data miners, researchers and investigators who want one quick pivot per indicator rather than a framework. Note the `--malware` module uploads the file to a third-party service — do not point it at confidential samples. The project has been quiet since 2023 and some third-party APIs it wraps may now require keys or have changed; its own docs live at docs.osint-spy.io.

## Typical use cases

### Run domain recon on a target

One flag, one target value.

```
osint-spy --domain target.example.com
```

Outcome: prints the aggregated domain recon results to the terminal; append `--json` to get machine-readable output for piping into a report or downstream tool.

### Pivot on an email address or IP

Turn a single identifier into WHOIS and related records.

```
osint-spy --email user@example.com
osint-spy --ip 198.51.100.10
```

Outcome: each command runs the corresponding lookup module and prints what it finds for that email address / IP address, giving you registration and reputation pivots from one line.

### Audit a server's TLS posture

Enumerate supported ciphers and check for Heartbleed.

```
osint-spy --ssl_cipher target.example.com
osint-spy --ssl_bleed target.example.com
```

Outcome: the first command lists the SSL/TLS ciphers the web server offers; the second reports whether it is vulnerable to Heartbleed (CVE-2014-0160) — a quick pre-engagement posture check from the same tool you are already using for recon.

### Look up a Bitcoin wallet

Pull the details of a BTC address (or the latest block) during a financial-investigation pivot.

```
osint-spy --btc_address 1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa
```

Outcome: prints the blockchain information recorded for that wallet address; `--btc_block` with no value reports the latest block, and `--btc_date 20250614` fetches block info by date.

> No corpus evidence — derived from upstream documentation.
