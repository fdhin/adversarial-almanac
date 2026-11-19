---
name: "cadaver"
categories:
  - web
version: "0.28-1"
homepage: "https://notroj.github.io/cadaver/"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2025-10-10"
build-system: autotools
license: GPL-2.0-only
meta-checked: "2026-11-09"
github-repo: "notroj/cadaver"
github-stars: 155
github-pushed: "2026-04-03"
github-archived: false
github-issues: 7
github-match: "search"
gh-checked: "2026-11-07"
worth: 5.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 3
gh-activity-checked: "2026-11-09"
---

# cadaver

> Command-line WebDAV client with an ftp-style interactive shell.

## What it's for

cadaver is the Unix command-line client for WebDAV: file upload and download, on-screen display, in-place editing of remote resources, namespace operations (move/copy), collection (directory) creation and deletion, property manipulation, and WebDAV locking. It deliberately mimics the BSD `ftp(1)` client — an interactive `dav:` prompt with familiar `ls`, `cd`, `put`, `get` commands — and supports automatic authentication from a `~/.netrc` file like ftp does. It is not a scanner or an enumerator; it is the tool for working with a WebDAV share you already know about (uploads to a server, editing files in a DAV-enabled CMS such as one exposing `/dav/`, verifying lock behaviour).

## Typical use cases

### Open a WebDAV share interactively

Connect and work in the ftp-style shell.

```
cadaver https://target.example.com/dav/
```

Outcome: prompts for credentials (or authenticates silently via `~/.netrc`) and drops you at a `dav:/dav/>` prompt where `ls`, `cd`, `put report.txt`, `get notes.txt`, `copy`, `move`, `mkcol newdir`, `delete`, `lock`/`unlock`, `propget`/`propset` and `edit` operate on remote resources; `help` lists every command and `quit` exits.

### Reach a WebDAV server through a proxy

Route the session via an HTTP proxy.

```
cadaver -p proxy.example.com:8080 https://target.example.com/dav/
```

Outcome: all WebDAV traffic for the session flows through the given proxy host:port; useful when the share is only reachable from a jump host or when you want the traffic in your proxy logs.

### Run with a prepared rcfile

Load per-site command defaults from a custom rcfile.

```
cadaver -r ~/.cadaverrc https://target.example.com/dav/
```

Outcome: commands and settings in the rcfile (keybindings, default options) are applied at startup instead of `~/.cadaverrc`; `-t` (`--tolerant`) additionally permits `cd`/`open` into collections on servers with broken WebDAV compliance.

No corpus evidence — derived from upstream documentation (README and packaged man page).
