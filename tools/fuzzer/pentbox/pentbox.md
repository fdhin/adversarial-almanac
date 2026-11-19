---
name: "pentbox"
categories:
  - blackarch-fuzzer
version: "1.8"
homepage: "http://www.pentbox.net"
packaged-by: [blackarch, aur]
blackarch-builddate: "2025-04-29"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "technicaldada/pentbox"
github-stars: 149
github-pushed: "2025-10-01"
github-archived: false
github-issues: 2
github-match: "search"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# pentbox

> Menu-driven Ruby security suite bundling network, password, DNS, web, and stress-testing tools behind one interactive prompt.

## What it's for

PentBox is a free security suite programmed in Ruby that runs anywhere Ruby is installed (GNU/Linux, Windows, macOS). From a single numbered menu it exposes network tools (TCP port scanner with SYN mode, ping), a honeypot that reports each attacker's IP and port, a hash cracker and multi-digest generator (MD5, SHA family, RIPEMD-160) with a bundled wordlist, DNS enumeration (NS/MX/PTR brute force, SHODAN lookups), web tools such as HTTP directory and common-file brute forcing plus MAC-address geolocation, and a set of DoS modules that are fenced behind a root check ("protected mode"). It is interactive rather than scriptable: tools are picked from menus and then prompted for their target — there is no flag-based CLI to automate.

## Typical use cases

### Launch the suite

```
pentbox
```

Outcome: starts the interactive menu; pick a category by number (network tools, password tools, web tools, ...) and then a specific tool, which prompts inline for the target and options. The honeypot entry, for instance, starts listening and prints the source IP and port of every connection it attracts.

### Run a shell command without leaving the menu

```
!id
```

Outcome: at any PentBox input prompt, a command prefixed with `!` (here `id`) executes in the underlying shell and drops you back into the menu, so host state can be checked mid-session without quitting.

*No corpus evidence — derived from upstream documentation.*
