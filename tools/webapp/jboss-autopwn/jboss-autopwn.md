---
name: "jboss-autopwn"
categories:
  - blackarch-webapp
version: "2.986085d"
homepage: "https://github.com/SpiderLabs/jboss-autopwn"
packaged-by: [blackarch, kali]
blackarch-builddate: "2025-12-01"
build-system: scripts
license: GPL-3.0-or-later
meta-checked: "2026-11-09"
github-repo: "SpiderLabs/jboss-autopwn"
github-stars: 177
github-pushed: "2020-08-07"
github-archived: true
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.8
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# jboss-autopwn

> SpiderLabs bash scripts that turn an exposed JBoss console into an interactive shell.

## What it's for

JBoss Autopwn (from SpiderLabs, no longer maintained) exploits JBoss AS servers with exposed management consoles: it authenticates to the JMX console, builds a JSP web shell into a WAR archive, deploys it, and then uses the shell's upload and command-execution capability to hand you an interactive session — a bind or reverse shell on Unix-like targets, or Meterpreter and VNC payloads on Windows. It targets the JBoss 3–6 era (roughly 2010–2013) and needs `curl`, netcat, and for the Windows script Metasploit-era payload tooling on the attacking box. For the same attack surface on modern engagements, JexBoss covers the same vectors with a maintained (if also dated) codebase.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Get a shell on a Linux/macOS JBoss target

Point the Unix script at an exposed JBoss HTTP listener.

```
jboss-autopwn-unix 198.51.100.10 8080
```

Outcome: the script grabs the JMX console cookie, crafts a BeanShell deployment, builds and deploys a WAR, and confirms code execution (`id`/`uname` output); it then asks interactively for a bind or reverse shell and port and drops you into that shell when the payload connects.

### Get Meterpreter or VNC on a Windows JBoss target

Use the Windows variant, which can run Metasploit payloads.

```
jboss-autopwn-win 198.51.100.10 8080
```

Outcome: same WAR-deployment chain as the Unix script but against a Windows host; when execution lands you are offered Metasploit Windows payloads (Meterpreter, VNC) instead of a plain netcat shell, and the deployed JSP shell remains at the printed URL for further manual work.
