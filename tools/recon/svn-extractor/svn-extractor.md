---
name: "svn-extractor"
categories:
  - blackarch-recon
version: "45.6829804"
homepage: "https://github.com/anantshri/svn-extractor"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-13"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "anantshri/svn-extractor"
github-stars: 482
github-pushed: "2024-01-28"
github-archived: false
github-issues: 7
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# svn-extractor

> Recovers source code, hidden file paths and commit usernames from an exposed .svn directory on a web server.

## What it's for

When a web deployment ships its Subversion metadata directory (.svn) readable over HTTP, that folder leaks the project's file inventory and pristine copies of every file. svn-extractor by Anant Shrivastava automates the recovery: it parses .svn/entries (SVN older than 1.6, plain text) or .svn/wc.db (SVN 1.7+, SQLite) to reconstruct the repository file list, then downloads the source of those files from the pristine store — .svn/pristine/<sha1-prefix>/<checksum>.svn-base — which also bypasses web-server restrictions such as htaccess deny rules placed on the original paths. Give it the URL of a site whose .svn folder is exposed; it is purely a web-based attack, nothing runs on the target.

## Typical use cases

### Dump everything from an exposed .svn

Full recovery of the repository contents.

```
svn-extractor --url http://target.example.com/.svn/
```

Outcome: detects whether metadata comes from entries or wc.db, walks the file list, downloads each recoverable file's pristine copy into the current directory, and prints progress; what lands locally is the site's source code.

### Inventory files without downloading

See the repository layout first.

```
svn-extractor --url http://target.example.com/.svn/ --noextract
```

Outcome: prints the hidden file and folder names the metadata reveals — often enough to spot backups, config files and admin paths — without fetching any file contents.

### Recover commit usernames

Identify developer accounts from version-control metadata.

```
svn-extractor --url http://target.example.com/.svn/ --userlist
```

Outcome: prints the usernames that have committed to the repository, useful as a seed list for credential attacks elsewhere.

### Download only matching files

Pull a chosen subset of the source.

```
svn-extractor --url http://target.example.com/.svn/ --match '\.php$'
```

Outcome: restricts downloads to files whose names match the regex, here all PHP source files.

*No corpus evidence — derived from upstream documentation.*
