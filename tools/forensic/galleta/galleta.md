---
name: "galleta"
categories:
  - blackarch-forensic
version: "20040505_1"
homepage: "https://www.jonesdykstra.com/"
packaged-by: [blackarch, kali, nixpkgs]
blackarch-builddate: "2025-12-14"
build-system: scripts
license: GPL-2.0-or-later
meta-checked: "2026-11-09"
worth: 1.1
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# galleta

> Parse Internet Explorer's classic Cookies index.dat into delimited records — URLs, names, values and FILETIME/UNIX timestamps.

## What it's for

Galleta (Keith J. Jones, Foundstone, 2003) examines Internet Explorer cookie files for forensic purposes. It reads the per-user `Cookies/index.dat` of classic IE (the pre-Vista file format from NT4 through IE6-era Windows XP), decodes every cookie record — visiting URL, cookie variable names and the values as stored, plus creation, expiry and last-accessed times in both FILETIME and UNIX epoch form and the per-record flags — and prints them to stdout as delimited rows, tab-separated by default. It targets only that legacy cookie format; modern Windows stores cookies in the ESE database (WebCache/INetCookies), which needs different tooling. There is no magic or search facility: it parses the file you name and that is all.

## Typical use cases

### Dump all cookies from a classic IE cookie file

Decode a seized user profile's cookie index.

```
galleta index.dat
```

Outcome: a header line naming the cookie file followed by one tab-delimited row per cookie record (URL, names/values, timestamps, flags) on stdout — redirect or scroll to review which sites set which cookies and when.

### Export cookies as CSV for a spreadsheet or timeline

Change the field delimiter for import into other tools.

```
galleta -d , index.dat > cookies.csv
```

Outcome: the same records emitted comma-delimited into `cookies.csv`, ready to open in a spreadsheet or merge into a timeline of user activity; `-d` accepts any single delimiter string (TAB remains the default when omitted).

*No corpus evidence — derived from upstream documentation.*
