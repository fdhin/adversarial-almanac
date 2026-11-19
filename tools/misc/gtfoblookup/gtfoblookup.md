---
name: "gtfoblookup"
categories:
  - blackarch-misc
version: "69.cc50e6e"
homepage: "https://github.com/nccgroup/GTFOBLookup"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "nccgroup/GTFOBLookup"
github-stars: 291
github-pushed: "2023-06-16"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# gtfoblookup

> Offline command-line lookup across GTFOBins, LOLBAS, WADComs and HijackLibs with category filtering.

## What it's for

GTFOBLookup keeps local copies of four attack-technique datasets and queries them from one CLI: GTFOBins (Unix binary abuse), LOLBAS (Windows signed-binary abuse), WADComs (AD attack techniques against Windows domains), and HijackLibs (DLL hijacking candidates). After a one-time `update` download it works fully offline, and its searches filter by category, type, prerequisite, OS, or vendor — so questions like "which SUID-able binaries give a shell" or "which vendors' DLLs are hijackable" are answerable without a browser. It is a lookup index, not a scanner: it knows nothing about your target host.

## Typical use cases

### Download the datasets for offline use

First-run step that clones local copies of all four supported repositories.

```
gtfoblookup update
```

Outcome: local copies of GTFOBins, LOLBAS, WADComs and HijackLibs are stored for offline searching; re-run to refresh them.

### Look up a binary's abuse techniques

Search GTFOBins for everything documented about one Unix executable.

```
gtfoblookup gtfobins search vim
```

Outcome: prints the GTFOBins entry for vim with its sudo, SUID, and shell categories and example commands.

### Search filtered by abuse category

Restrict a search to binaries usable through specific categories, comma-separated.

```
gtfoblookup gtfobins search -c sudo,suid find
```

Outcome: prints only the find techniques that apply under the sudo and SUID categories.

### Look up a Windows executable on LOLBAS

Query the Windows counterpart dataset for a signed executable.

```
gtfoblookup lolbas search certutil
```

Outcome: prints the LOLBAS entry for certutil with its documented abuse types and example commands.

### Discover valid filter values

List the attribute values a dataset can be filtered by before searching.

```
gtfoblookup gtfobins list categories
```

Outcome: prints every category featured in the local GTFOBins copy (shell, sudo, suid, ...), usable with `search -c`; other attributes such as executables or OSs work the same way.

*No corpus evidence — derived from upstream documentation.*
