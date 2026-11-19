---
name: "gocrack"
categories:
  - cracker
homepage: "https://github.com/mandiant/goCrack"
github-repo: "mandiant/goCrack"
github-match: "exact"
origin: "awesome-pentest"
recipe: "upstream"
license: MIT
github-stars: 1290
gh-checked: "2026-11-14"
worth: 5.9
worth-basis: "full (activity+popularity+recency+adoption)"
packaged-by: []
---

# gocrack

> Distributed password-cracking orchestration — a Go server, web UI and GPU workers that manage hashcat tasks, dictionaries, rules and masks for a cracking team.

## What it's for

GoCrack (Mandiant; the repo moved from the fireeye/ organization to mandiant/) provides APIs and a web interface to manage password-cracking tasks across supported engines — currently hashcat 6.x+, linked as a shared library. The server holds users, tasks and the engine files (dictionaries, mangling rules, brute-force masks); GPU workers authenticate to it and pull tasks, so an engagement's cracking capacity pools many machines behind one queue instead of ad-hoc ssh-and-screen sessions. Engine files upload once and are reused across tasks, with sharing controls — non-admins can use a shared dictionary in their own tasks and see its metadata but cannot edit or download the contents. Authentication comes from the local database or LDAP, and tasks report progress and cracked results back into the UI. Think Hashtopolis-class workflow: upload once, dispatch everywhere, watch progress. It is the management layer only — the actual cracking runs in hashcat on the workers, so workers want NVIDIA/AMD GPUs with a working OpenCL stack. MIT, written in Go.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Take first possession of a fresh install

```
Web UI → log in → change the admin password
```

Outcome: With the database auth plugin the server starts with an `admin` account using a documented default password (`ch@ng3me!`) that must be changed immediately; with the LDAP plugin the first user to log in is promoted to admin instead.

### Upload dictionaries, rules and masks

```
File Manager → Engine Files → Upload File → pick type → tick Shared
```

Outcome: Makes a wordlist, rules file or masks file available to tasks — tasks stay non-functional until at least one dictionary and one masks file are uploaded; Shared exposes the file to non-admins for use in tasks without revealing its contents.

### Build server and workers with chosen backends

```
make SERVBUILDTAGS="auth_database auth_ldap"
```

Outcome: Builds the gocrack server and worker binaries with both the database and LDAP authentication providers compiled in — the default build includes all supported modules, so list fewer tags to strip backends you do not want to ship.
