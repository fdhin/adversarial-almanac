---
name: "githack"
categories:
  - blackarch-recon
version: "16.a3d70b1"
homepage: "https://github.com/lijiejie/githack"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "lijiejie/githack"
github-stars: 3580
github-pushed: "2023-02-01"
github-archived: false
github-issues: 17
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# githack

> Exploits a publicly readable `.git` directory on a web server by downloading the repository objects and reconstructing the source tree locally.

## What it's for

When a deployment leaves `/.git/` reachable, the repository's index and object store are exposed. GitHack downloads the `.git` folder's files from the web server, parses `.git/index` to enumerate every (filename, blob SHA-1) pair, fetches the corresponding objects from `.git/objects/`, decompresses them, and writes the source code back out with the original directory structure intact. The output is a working copy of the project ready for offline auditing — credentials in config files, SQL injection, file-upload logic. It only reads what the server already serves over HTTP; if object files are blocked or the index is missing, recovery will be partial.

## Typical use cases

### Rebuild source code from a disclosed .git folder

Point it at a web root whose `.git` directory is accessible.

```
githack http://target.example.com/.git/
```

Outcome: fetches the git index and objects over HTTP, then writes the reconstructed source tree into a local directory named after the host (`target.example.com/`), preserving the project's original layout — audit it for hardcoded credentials and vulnerabilities. Requires the trailing `/.git/` in the URL.

*No corpus evidence — derived from upstream documentation.*
