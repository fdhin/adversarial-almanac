---
name: "wpa-bruteforcer"
categories:
  - blackarch-wireless
version: "4.d5f8586"
homepage: "https://github.com/SYWorks/wpa-bruteforcer"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-26"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "SYWorks/wpa-bruteforcer"
github-stars: 53
github-pushed: "2014-01-07"
github-archived: false
github-issues: 1
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# wpa-bruteforcer

> Dictionary-testing of WPA/WPA2 access points without needing a connected client (SYWorks).

## What it's for

WPA-BruteForcer (by SY Chua / SYWorks, 2014) is a Python console tool for testing WPA/WPA2 pre-shared keys against an access point directly, without first having to capture a handshake from a legitimate client. It drives a monitor-mode interface, attacks a chosen ESSID with a wordlist (defaulting to `/usr/share/john/password.lst`), and can randomise or set its own MAC address before transmitting. It is menu-driven when launched bare — you pick interface, ESSID, dictionary and timeout interactively — and the same parameters can be given as command-line flags for non-interactive runs. It is a 2014 Python 2 tool from the pre-WPA3 era; expect it as an audit checkbox against legacy PSK deployments, not a modern cracking workflow.

## Typical use cases

### Run an interactive attack session

Launch with no arguments to get the guided console flow.

```
sudo wpa-bruteforcer
```

Outcome: opens the interactive menus that step through interface selection, target ESSID, dictionary path and timeout, then runs the dictionary attack and reports the result. Root privileges are required.

### Specify the target non-interactively

Give interface, timeout and ESSID on the command line.

```
sudo wpa-bruteforcer -i wlan0mon -t 15 -e "Example-AP"
```

Outcome: skips the corresponding interactive prompts and starts testing `Example-AP` through `wlan0mon` with a 15-second window; `-d passwords.lst` selects a different wordlist, and `--iface`/`--essid`/`--dict`/`--timeout` are the long forms.

### Randomise the source MAC before attacking

Spoof the attacking interface's MAC automatically.

```
sudo wpa-bruteforcer -i wlan0mon -e "Example-AP" -d passwords.lst --spoof
```

Outcome: the interface's MAC address is randomised before the attack begins; `--mac 00:01:02:03:04:05` sets a specific address instead of a random one.

*No corpus evidence — derived from upstream documentation.*
