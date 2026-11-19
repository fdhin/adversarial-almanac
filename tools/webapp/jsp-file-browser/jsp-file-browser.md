---
name: "jsp-file-browser"
categories:
  - webapp
version: "1.2-0parrot1"
homepage: "https://www.vonloesch.de/filebrowser.html"
origin: "parrot"
recipe: "parrot-repo"
packaged-by: [parrot]
build-system: scripts
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# jsp-file-browser

> Single-file JSP web application for remote file management and native command execution on the server.

## What it's for

Jsp File Browser (by vonloesch.de, version 1.2 from 2006, GPL) is exactly one `Browser.jsp` file: drop it into any JSP 1.1+ application (tested on Tomcat 4/5.5, Resin, Jetty) and it serves a web UI for browsing, creating, copying, moving, renaming and deleting files and directories; viewing pictures/PDFs/HTML; editing text files; uploading with a progress monitor; downloading files or whole folders as on-the-fly ZIPs; unpacking zip/jar/war/gz archives; and executing native shell commands. It is dual-use by design — a legitimate remote-administration aid for JSP hosts, and equally a classic post-exploitation file manager. Behavior is locked in by constants at the top of the JSP (`READ_ONLY`, `NATIVE_COMMANDS`, `ALLOW_UPLOAD`, `RESTRICT_BROWSING`/`RESTRICT_WHITELIST` with `RESTRICT_PATH`), so auditing a found copy is a matter of reading those switches.

No corpus evidence — derived from upstream documentation (upstream `Browser.jsp` source inspected).

## Typical use cases

### Audit a found Browser.jsp deployment

Check which capabilities the deployed copy has switched on.

```
grep -n "private static final" Browser.jsp
```

Outcome: prints the configuration constant block of the page — whether native command execution (`NATIVE_COMMANDS`), uploads (`ALLOW_UPLOAD`) and writes (`READ_ONLY`) are enabled, and whether browsing is restricted to `RESTRICT_PATH` entries — which tells you immediately how dangerous (or defanged) the copy you found is.

### Enumerate its URL-reachable actions

Map the page's HTTP interface for review.

```
grep -n 'request.getParameter' Browser.jsp
```

Outcome: lists every request parameter the page acts on — `dir`, `sort`, `command` (native command execution), `editfile`/`text` (file editing), `nfile`/`cr_dir`/`new_name` (file and directory creation/renaming), `downfile`, `unpackfile` (archive extraction) — the exact surface to test or block at the web-application firewall when one of these pages is exposed.
