---
name: "linset"
categories:
  - blackarch-automation
version: "9.8746b1f"
homepage: "https://github.com/vk496/linset"
packaged-by: [blackarch, aur]
blackarch-builddate: "2016-12-31"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "vk496/linset"
github-stars: 577
github-pushed: "2016-02-21"
github-archived: false
github-issues: 39
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# linset

> Menu-driven evil-twin attack that tricks users into revealing the WPA/WPA2 password.

## What it's for

Linset ("Linset Is Not a Social Engineering Tool") automates the full evil-twin chain against a WPA/WPA2 network: it scans for targets, captures or reuses a handshake, clones the access point, stands up DHCP, DNS and a router-lookalike web page on the fake AP, deauthenticates clients to herd them onto it, and checks every password a victim types into the page against the captured handshake — stopping when the correct one is submitted. It is a single interactive bash script (root required, monitor-mode interface plus the aircrack-ng suite, hostapd/airbase-ng, dhcpd, mdk3 and php-cgi are checked at startup). Development stopped in 2016 and the vendor-specific web interfaces were removed for copyright reasons, so only a generic frontend ships. Only use against networks you own or are authorized to test.

## Typical use cases

### Launch the guided attack workflow

Run the script and follow the menus.

```
sudo ./linset
```

Outcome: after a dependency check, presents the interactive menu chain — select wireless interface, scan, choose target network, verify/reuse a handshake, choose web interface — then spins up the fake AP, DHCP, DNS and web server and starts deauthenticating clients; when a victim submits the correct password in the captive page, the attack halts and the recovered password is saved to a file in your home directory.

*No corpus evidence — derived from upstream documentation.*
