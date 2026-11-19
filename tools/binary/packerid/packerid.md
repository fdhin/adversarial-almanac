---
name: "packerid"
categories:
  - blackarch-binary
version: "16.bc54e6d"
homepage: "https://github.com/sooshie/packerid"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "sooshie/packerid"
github-stars: 50
github-pushed: "2020-04-01"
github-archived: false
github-issues: 4
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# packerid

> Identifies the packer or protector used on a Windows PE file by matching it against a PEiD signature database, with JSON output, signature extraction, and entry-point disassembly.

## What it's for

Use this during the triage of a suspicious Windows executable to answer "is this packed, and with what?" before you attempt static analysis. It is a fork of the classic `packerid.py` that matches the PE file (entry point and section data) against PEiD-style signatures from a `userdb.txt` database and prints the packer that best fits. This fork adds extra output formats (terse listing, JSON), digital-signature extraction, and Capstone-based disassembly of the entry point. It only identifies packers from signatures — it does not unpack or decompress anything. It depends on the `pefile` Python module and Capstone.

## Typical use cases

### Identify the packer of a PE file

Match the binary against the bundled PEiD signature database and print the detected packer.

```
packerid -P sample.exe
```

Outcome: prints the PEiD signature matches for the file (packer/protector name), telling you which packing layer to expect before further analysis.

### Show every matching signature

List all signatures that match, not just the best candidate.

```
packerid -P -m sample.exe
```

Outcome: prints all signature matches in the database, useful when a file matches several packers or when the top hit looks like a false positive.

### Machine-readable output for pipelines

Emit the same analysis as JSON for scripted triage.

```
packerid -J -p sample.exe
```

Outcome: prints a pretty-printed JSON document with the PE properties and matches (`-J` selects JSON, `-p` pretty-prints it), which can be piped into `jq` or stored in a report.

### Use a custom signature database

Substitute your own PEiD-format signature file for the bundled one.

```
packerid -P -D custom-userdb.txt sample.exe
```

Outcome: matches are computed against `custom-userdb.txt` instead of the built-in database, so updated or proprietary signatures can be used.

### Disassemble the entry point

Show the first instructions executed by the binary, mapped at the entry point.

```
packerid -d 32 sample.exe
```

Outcome: prints a Capstone disassembly of the first 32 bytes after the entry point, which for packed binaries typically shows the unpacking stub instead of original program code.

### Extract the Authenticode digital signature

Pull the digital signature block out of a signed PE file.

```
packerid -e signed.exe
```

Outcome: extracts and prints the digital signature information of the file, letting you inspect the signer certificate data during triage.

*No corpus evidence — derived from upstream documentation.*
