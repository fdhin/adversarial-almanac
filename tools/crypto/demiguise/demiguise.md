---
name: "demiguise"
categories:
  - blackarch-crypto
version: "11.58d5681"
homepage: "https://github.com/nccgroup/demiguise"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: AGPL
meta-checked: "2026-11-09"
github-repo: "nccgroup/demiguise"
github-stars: 1431
github-pushed: "2022-11-09"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# demiguise

> Generates HTML pages that carry an RC4-encrypted HTA payload and decrypt it in the browser.

## What it's for

demiguise (NCC Group) is a red-team delivery wrapper: it RC4-encrypts an HTA (HTML Application) payload and embeds it in an ordinary HTML page whose JavaScript decrypts the blob in the browser and hands it to the user via `navigator.msSaveBlob` — the HTA never crosses the network in plaintext and content-inspecting proxies see only `text/html`. Its second purpose is environmental keying: derive the key from something bound to the real target (e.g. their external IP, as in the bundled `examples/externalip.js`) and sandboxes that fetch the page get garbage. It ships a handful of COM-execution payload templates (Outlook.Application, Excel.RegisterXLL, WbemScripting.SWbemLocator, ShellBrowserWindow) plus a PlainFile dropper, but the payload content itself is yours to template; this is an evasion/delivery tool, not an exploit or payload generator.

## Typical use cases

### List the payload types

```
demiguise -l
```

Outcome: prints `[*] Payload choices:` followed by the available payload types (the COM-execution templates and `PlainFile`), then exits without writing anything.

### Generate an encrypted HTA-delivering page

```
demiguise -k hello -c "notepad.exe" -p Outlook.Application -o test.hta
```

Outcome: RC4-encrypts the payload under the key `hello` and writes `test.html` — an HTML page that, when visited, decrypts itself and executes `notepad.exe` via the Outlook.Application COM technique rather than as a child of mshta.exe. The key is embedded in the generated JavaScript; replace it with the environmental-keying JS from `examples/` to derive the key at runtime instead.

### Drop an arbitrary file instead of running a COM payload

```
demiguise -k s3cr3tK3y -p PlainFile -f implant.hta -o page.html
```

Outcome: base64+RC4-encrypts `implant.hta` into `implant.html`, which decrypts it in-browser and offers it as a download under its original filename — a way to smuggle any file type past file-type inspection.

*No corpus evidence — derived from upstream documentation.*
