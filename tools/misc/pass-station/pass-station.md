---
name: "pass-station"
categories:
  - blackarch-misc
version: "v3.0.0.r34.g315a7e0"
homepage: "https://noraj.github.io/pass-station/"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-31"
build-system: ruby
license: MIT
meta-checked: "2026-11-09"
github-repo: "noraj/pass-station"
github-stars: 58
github-pushed: "2026-11-08"
github-archived: false
github-issues: 0
github-match: "search"
gh-checked: "2026-11-09"
worth: 6.6
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 40
gh-activity-checked: "2026-11-09"
---

# pass-station

> CLI and Ruby library for looking up factory default credentials by product or vendor.

## What it's for

Pass Station searches a bundled database of thousands of default username/password pairs across products and vendors — the classic "which credentials does this device or web panel ship with" lookup during network and web assessments. It supports regex search, per-column filtering and sorting, case sensitivity, and table/JSON/YAML/CSV output, and can switch between two source databases (DefaultCreds-cheat-sheet and Many Passwords). It only reports what a product's default credentials are; it never attempts a login itself. Development moved from the original SEC-IT repository (archived 2021) to the author's actively maintained fork at `noraj/pass-station`, which is what distro packages build.

## Typical use cases

### Look up a product's default credentials

```
pass-station search tomcat
```

Outcome: case-insensitive matches across the vendor/product/username/password columns, printed as a colorized pretty table.

### Restrict the search to one column

```
pass-station search WEBLOGIC --field username --sensitive
```

Outcome: only rows whose username column equals `WEBLOGIC` exactly, still rendered as a table for direct use against a login form.

### Regex across every column

```
pass-station search 'admin[0-9]+' --field all
```

Outcome: rows where any column matches the regular expression — useful for enumerating numbered default accounts such as `admin1`, `admin2`.

### Dump the database as machine-readable output

```
pass-station list --sort username --output json
```

Outcome: the whole default-credential table sorted by username as JSON on stdout, ready to pipe into `jq` or import into another tool.

### Check whether the bundled database is stale

```
pass-station update --check
```

Outcome: compares the local database hash against the upstream source and reports whether an update is available; `pass-station update --force <path>` then replaces the local copy.

*No corpus evidence — derived from upstream documentation.*
