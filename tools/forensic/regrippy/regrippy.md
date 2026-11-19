---
name: "regrippy"
categories:
  - blackarch-forensic
version: "2.0.0"
homepage: "https://pypi.org/project/regrippy/#files"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-13"
build-system: python
license: Apache
meta-checked: "2026-11-09"
github-repo: "airbus-cert/regrippy"
github-stars: 219
github-pushed: "2026-05-12"
github-archived: false
github-issues: 3
github-match: "search"
gh-checked: "2026-11-07"
worth: 5.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 2
gh-activity-checked: "2026-11-09"
---

# regrippy

> Modern Python 3 alternative to RegRipper from the Airbus CERT: a plugin framework for quick registry-hive triage.

## What it's for

RegRippy (by the Airbus Computer Emergency Response Team) reads Windows registry hives through Willi Ballenthin's python-registry and extracts common forensic artifacts via named plugins (`compname`, `typedurls`, persistence listings, ...), the way RegRipper does but in modern Python 3. Its design goal is incident-response speed: point it at a mounted evidence tree (`--root`) instead of individual hive paths, run one plugin at a time, and get human-readable or body-file (mactime-ready) output. It is also a framework for writing your own plugins quickly. It focuses on single-artifact extraction and IR workflows rather than full-hive dumps.

## Typical use cases

### Extract one artifact from a mounted evidence drive

Give it the Windows directory root and a plugin name.

```
regrip.py --root /mnt/evidence/C compname
```

Outcome: the plugin locates the hives it needs under the root and prints the artifact — here the machine's computer name (e.g. `JOHN-DESKTOP`).

### Iterate over every user's hive

Run a plugin against all NTUSER.DAT/UsrClass.DAT files at once.

```
regrip.py -v --root /mnt/evidence/C --all-user-hives typedurls
```

Outcome: the plugin runs per user; output is prefixed with the account name and warnings flag users missing the relevant key (e.g. `regrip.py:info:Administrator`).

### Emit body-file lines for a timeline

Force machine-readable output that mactime can consume.

```
regrip.py --root /mnt/evidence/C --bodyfile typedurls >> timeline.body
```

Outcome: each artifact is emitted as a body-file line (MACB timestamps + path) appended to your timeline body file instead of the human-readable format.

### Target hives directly without a mounted tree

Specify exact hive paths when the evidence is loose files.

```
regrip.py --system /mnt/evidence/SYSTEM --software /mnt/evidence/SOFTWARE compname
```

Outcome: the plugin reads the explicitly provided hives (`--system`, `--software`, `--sam`, `--ntuser`, `--usrclass`) instead of searching a root directory.

### Discover available plugins

List what the installed version can extract.

```
regrip.py --list
```

Outcome: prints all plugin names available for the commands above; each plugin is also callable directly via its `reg_<name>` symlink (e.g. `reg_compname -r /mnt/evidence/C`).

*No corpus evidence — derived from upstream documentation.*
