---
name: "wscript"
categories:
  - blackarch-code-audit
version: "201.0410be2"
homepage: "https://github.com/mrpapercut/wscript"
packaged-by: [blackarch]
blackarch-builddate: "2019-07-25"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "mrpapercut/wscript"
github-stars: 82
github-pushed: "2019-07-23"
github-archived: true
github-issues: 6
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.6
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wscript

> Browser-based Windows Script Host emulator: runs JScript WSH files against a pure-JavaScript reimplementation of the WScript object model and traces every call, with no Windows host and no real side effects.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Safely triaging WSH scripts — droppers, downloaders, and the `.js`/`.jse` files that arrive in phishing attachments — without a Windows VM. The tool is a web page: you load a JScript file into it, and it executes the script unmodified against a JavaScript port of the WScript object model (`WshShell`, `WshNetwork`, `Scripting.FileSystemObject`, `ADODB.Stream`, `MSXML.XMLHTTP`, and friends), including WSH quirks like case-insensitive identifiers. While it runs, a tracer records every function call and property access — obfuscation (even JSFuck-grade) does not hide the trace because the original script runs as-is — and a mock VFS lists the files the script would have created, modified or deleted. There are no real side effects: HTTP downloads are simulated (requested URL and would-be save path shown, nothing fetched), and no registry or filesystem changes happen. Limitations to know: JScript only — VBScript files are not supported — and a few object models (Microsoft.XMLDOM, ApplicationObject) are not implemented, so scripts touching them stop there.

## Typical use cases

### Open the emulator from the packaged launcher

```
wscript
```

Outcome: the prebuilt emulator page (`file:///usr/share/wscript/dist/index.html`) opens in your default browser via `xdg-open`; on a headless box the same message prints the URL to open manually. Load a suspicious `.js` file in the page and watch the tracer panel populate with every call and the VFS panel with the filesystem the script would have touched.

### Open the built emulator page directly

```
xdg-open dist/index.html
```

Outcome: opens the same emulator UI from the package's built `dist/` directory without the wrapper — the emulator is plain client-side JavaScript, so any modern browser works and no server component is involved.
