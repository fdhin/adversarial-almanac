---
name: "w3af"
categories:
  - blackarch-fuzzer
version: "1.6.49"
homepage: "https://github.com/andresriancho/w3af/releases"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL
meta-checked: "2026-11-09"
github-repo: "andresriancho/w3af"
github-stars: 4911
github-pushed: "2023-02-22"
github-archived: false
github-issues: 2033
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# w3af

> Classic plugin-driven web application security scanner and attack framework.

## What it's for

w3af (Andres Riancho) identifies and helps exploit 200+ web application vulnerabilities — cross-site scripting, SQL injection, OS commanding and many more — through composable plugin categories: crawl, audit, grep, bruteforce, auth, mangle, evasion and output. You configure a target, plugin set and HTTP settings in its console (`w3af_console`) or GUI, then `start` the scan; findings accumulate in a knowledge base that the built-in exploit and shell plugins can act on directly. Development stopped in 2023, so coverage of modern JavaScript-heavy applications lags current scanners; it remains strong on classic server-rendered targets and as a scriptable audit framework with reusable profiles.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Batch scan with a bundled profile

Run a canned scan non-interactively against one target.

```
./w3af_console -P profiles/fast_scan.pw3af -t http://target.example.com/ -y -n
```

Outcome: loads the `fast_scan` profile's crawl/audit/grep plugin set, accepts the disclaimer (`-y`), skips the update check (`-n`) and prints every finding to the console as the scan runs, without opening a prompt. Other bundled profiles include `OWASP_TOP10`, `full_audit` and `bruteforce`.

### Script a scan with a command file

Reproduce an exact scan by feeding console commands from a file.

```
printf 'target set target http://target.example.com/\nplugins\naudit xss, sqli\nback\nstart\n' > scan.w3af
./w3af_console -s scan.w3af -n -y
```

Outcome: executes the script's console commands in order — set the target, open the plugins menu, enable the `xss` and `sqli` audit plugins, return, start the scan — producing the same findings output as an equivalent interactive session, ideal for regression-testing a target.

### Drive an interactive audit session

Explore plugins, settings and results by hand.

```
./w3af_console
target set target http://target.example.com/
plugins
audit xss, sqli, os_commanding
back
start
```

Outcome: opens the console prompt, configures the target, enables three audit plugins from the `plugins` submenu, and `start` launches the scan with live findings output; afterwards the `kb` menu lists everything found and the `exploit` menu can act on findings.
