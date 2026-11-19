---
name: "pdblaster"
categories:
  - blackarch-forensic
version: "4.fc8abb3"
homepage: "https://github.com/SecurityRiskAdvisors/PDBlaster"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "SecurityRiskAdvisors/PDBlaster"
github-stars: 50
github-pushed: "2020-01-13"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pdblaster

> Bulk-extract PDB debug paths from large sets of executables to surface attacker usernames, build paths and repeated compiler artifacts.

## What it's for

Windows PE files compiled with debug information carry a PDB path that often embeds the developer's username, project name and build folder. PDBlaster (Security Risk Advisors) walks a directory of samples, pulls each PE's PDB path (via pefile), and turns the results into a CSV dataset with basic repeat and username analysis — the workflow described in threat-intel research on attacker breadcrumbs left in malware. It does not download the PDB files themselves: it mines the paths recorded inside the binaries. Reach for it when you have hundreds or thousands of executables and want the compiler-attribution artifacts out of them in one pass; a `-p` pause interval throttles the walk on huge sets, and `--sherlock` can feed discovered usernames to an account-lookup checker.

## Typical use cases

### Extract PDB paths from a sample set into CSV

```
pdblaster.py -s /samples -o /results/pdb_paths.csv
```

Outcome: iterates the executables under the sample directory, records each file's PDB path details, and writes the dataset to the CSV; progress is printed as it goes, pausing every 500 files when `-p` is set.

### Surface repeated usernames and paths across samples

```
pdblaster.py -s /samples -o /results/pdb_paths.csv -u -r --summary
```

Outcome: `-u` prints the usernames recovered from PDB paths, `-r` reports PDB paths and usernames that repeat across samples (attribution leads), and `--summary` prints an overview of the samples that yielded PDB paths at all.

*No corpus evidence — derived from upstream documentation.*
