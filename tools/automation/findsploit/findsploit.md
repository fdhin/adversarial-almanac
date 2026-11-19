---
name: "findsploit"
categories:
  - blackarch-automation
version: "87.3e61d8d"
homepage: "https://github.com/1N3/findsploit"
packaged-by: [blackarch]
blackarch-builddate: "2021-10-03"
build-system: scripts
license: custom:unknown
meta-checked: "2026-11-09"
github-repo: "1N3/findsploit"
github-stars: 1865
github-pushed: "2021-09-27"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# findsploit

> Search the local exploit databases — exploit-db, Metasploit modules, Nmap NSE scripts — from one command.

## What it's for

findsploit is a thin bash wrapper that searches the exploit collections already on disk (exploit-db, the Metasploit module tree, and Nmap NSE scripts) for one or more terms and prints the matching paths, turning "is there a public exploit for X?" into a single shell command instead of grepping directory trees by hand. The same package ships two companion helpers: `copysploit` copies an exploit-db file into the current directory, and `compilesploit` compiles and runs a C exploit. It only searches what exists locally — it does not query online services — so refresh its underlying databases with your package manager separately.

## Typical use cases

### Search for exploits by product

Find every public exploit matching a term.

```
findsploit apache
```

Outcome: all exploit-db exploits and Metasploit modules matching `apache`, printed with file paths ready to open or copy.

### Combine search terms

Narrow from product to a specific version.

```
findsploit apache 2.4.49
```

Outcome: only matches containing both terms — the quick jump from "product X" to "the exploit for the exact version I found".

### Inventory Metasploit or Nmap holdings

List a whole collection and filter it.

```
findsploit payloads | grep windows
findsploit nmap | grep smb
```

Outcome: the full Metasploit payload list filtered to Windows payloads, and the full NSE script list filtered to SMB; `exploits`, `auxiliary`, and `encoder` work the same way.

### Copy and compile an exploit

Pull an exploit out of the database and build it.

```
copysploit 1337.c && compilesploit 1337.c
```

Outcome: exploit-db file 1337.c lands in the current directory, is compiled, and the resulting binary is executed.

### Update the tool

Pull the latest findsploit itself.

```
findsploit -u
```

Outcome: checks upstream for a newer findsploit release and installs it on confirmation (the long form is `--update`).

*No corpus evidence — derived from upstream documentation.*
