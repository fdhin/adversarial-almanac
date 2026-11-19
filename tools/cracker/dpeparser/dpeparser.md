---
name: "dpeparser"
categories:
  - blackarch-cracker
version: "beta002"
homepage: "http://www.toolswatch.org/dpe/"
packaged-by: [blackarch]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "Seabreg/DPEparser"
github-stars: 0
github-pushed: "2019-05-05"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-07"
worth: 1.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# dpeparser

> Queries the Default Password Enumeration (DPE) XML database for vendor default credentials by vendor, device type, CPE or description.

*No corpus evidence — derived from upstream documentation.*

## What it's for

dpeparser is the client for ToolsWatch's Default Password Enumeration project: a curated XML database (`dpe_db.xml`) of factory default credentials mapped to vendors, device types and CPE names. Instead of grepping a web page, you ask it for every default credential set a vendor has shipped — the natural first check before any brute force, since device owners rarely change defaults. It is a read-only lookup tool (Python 2, single file): it does not scan networks or try credentials anywhere. The `-u` option originally refreshed the XML database from the project site, but toolswatch.org's DPE endpoint is offline, so the database bundled with the package is the working copy.

## Typical use cases

### List a vendor's default credentials

Check what a vendor ships as factory logins.

```
dpeparser -v cisco
```

Outcome: prints every DPE entry whose vendor matches `cisco` — device model/description, CPE name and the default username/password pairs recorded for it — then `[+] Exiting program.` Works entirely offline against the local `dpe_db.xml`.

### Find defaults by device type

Broaden the search to a class of device.

```
dpeparser -t router
```

Outcome: prints all database entries typed `router` across every vendor (also accepts `switch`, `hub`, `printer`, ...) with their default credentials.

### Look up one device by CPE

Match a CPE string collected from an inventory scan or banner.

```
dpeparser -c cpe:/h:cisco:router_4000
```

Outcome: returns the default credentials recorded for that CPE-identified hardware entry, mapping an asset-inventory CPE directly to its factory logins.

### Search free-text descriptions

Find devices by model words instead of structured fields.

```
dpeparser -d 'cisco router 2600'
```

Outcome: prints entries whose description matches the phrase — useful when the vendor name is unknown but the model line from a sticker or banner is available. Run `dpeparser -b` to print the banner including the database build date it is searching.
