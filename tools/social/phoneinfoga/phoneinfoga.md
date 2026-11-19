---
name: "phoneinfoga"
categories:
  - blackarch-social
version: "v2.11.0.r6.g55807b0"
homepage: "https://github.com/sundowndev/PhoneInfoga"
packaged-by: [blackarch]
blackarch-builddate: "2026-09-06"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "sundowndev/PhoneInfoga"
github-stars: 18421
github-pushed: "2026-08-25"
github-archived: false
github-issues: 169
github-match: "exact"
gh-checked: "2026-11-07"
worth: 6.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 4
gh-activity-checked: "2026-11-09"
---

# phoneinfoga

> Information gathering & OSINT framework for phone numbers.

## What it's for

PhoneInfoga (v2, Go) scans international phone numbers: it first resolves
structured facts — country, area, carrier, line type — from libphonenumber,
then runs optional scanners that footprint the number across external
APIs, phone books and search engines to surface reputation reports, social
media accounts and disposable-number providers. Scanners that need
external services must be configured or they silently stay off; the tool
deliberately does not verify or geolocate anything — it gathers leads for
manual analysis. The upstream project is stable but unmaintained. It
cannot track a phone, obtain precise locations, or "hack" a number.

## Typical use cases

### Scan a single number

Basic structured recon of one international number.

```
phoneinfoga scan -n "+1 555 444 3333"
```

Outcome: Prints the number's validity, country, carrier and line type,
plus the results of every configured scanner. Special characters like
`( ) - +` are tolerated; the country code is required.

### Run a specific scanner only

Footprint a number without running the full scanner set.

```
phoneinfoga scan -n +428****4833 -s footprints
```

Outcome: Runs only the `footprints` scanner against the number and reports
the footprints it finds; useful when most scanners are unconfigured.

### Batch scan numbers from a file

Process a list of leads at once.

```
phoneinfoga scan -i numbers.txt -o results.txt
```

Outcome: Scans every line of the input file (one number per line, invalid
entries skipped) and writes the combined results to the output file.

### Try every custom number format

Discover footprints when you don't know the local formatting.

```
phoneinfoga recon -n +428****4833
```

Outcome: Generates custom number formats derived from the number's country
code and checks each, reporting which formats yield footprints.

### Serve the web UI and REST API

Run scans from a browser or programmatically.

```
phoneinfoga serve -p 8080
```

Outcome: Starts the stateless web client plus REST API on port 8080
(default 5000); open `http://localhost:8080` to run scans from the
browser. Add `--no-client` to expose the REST API alone.

*No corpus evidence — derived from upstream documentation.*
