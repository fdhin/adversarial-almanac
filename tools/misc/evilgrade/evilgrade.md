---
name: "evilgrade"
categories:
  - blackarch-misc
version: "2.0.9"
homepage: "https://github.com/infobyte/evilgrade"
packaged-by: [blackarch, aur]
blackarch-builddate: "2025-07-18"
build-system: perl
license: GPL
aliases: ["isr-evilgrade"]
meta-checked: "2026-11-09"
github-repo: "infobyte/evilgrade"
github-stars: 1326
github-pushed: "2021-09-01"
github-archived: false
github-issues: 11
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.2
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# evilgrade

> Modular framework that hijacks software update checks and serves your payload as the "update".

*No corpus evidence — derived from upstream documentation.*

## What it's for

evilgrade (ISR-evilgrade, by Francisco Amaro of Infobyte) exploits applications with poorly implemented update mechanisms. It ships roughly 60 modules, each emulating a specific application's update service (Java, Notepad++, WinSCP, Apple Update, aMSN, Windows Update paths, and more), plus built-in web and DNS servers. The premise is that you can already spoof name resolution for the victim — via ARP spoofing, DHCP spoofing, DNS cache poisoning, or a rogue access point. Once the victim's application polls for updates, evilgrade answers and delivers your chosen binary (the "agent") as the update. It is a delivery mechanism only: it neither creates the man-in-the-middle network position nor the payload — you supply both. Cross-platform in principle; the packaged modules largely target 2008-2014-era Windows update clients, so many are historical.

## Typical use cases

### List available update-spoofing modules

See which applications the framework can impersonate.

```
evilgrade
```

Then at the console prompt:

```
evilgrade> show modules
```

Outcome: starts the interactive (IOS-like) console and prints the list of implemented modules with a count — pick the module matching software actually installed on the victim.

### Configure a module and point it at a payload

Arm an update emulation with your agent binary.

```
evilgrade> conf sunjava
evilgrade(sunjava)> set agent ./agent/payload.exe
evilgrade(sunjava)> show options
```

Outcome: enters module configuration context, sets the fake update binary to serve, and prints the module's options table (VirtualHost domains emulated, update title/description shown to the victim, agent path, enable flag) so you can verify everything before going live.

### Start the web and DNS servers

Begin answering the victim's update traffic.

```
evilgrade> start
```

Outcome: starts the built-in WebServer and DNSServer ("Webserver ready. Waiting for connections..."); as victim applications poll, each request and the "Agent sent" delivery is logged to the console in real time.

### Check server state and victim activity

Review what has been served so far.

```
evilgrade> show status
```

Outcome: reports the webserver PID and per-victim status/log lines (seen on the `active`/`status` objects); `stop` shuts the services down again.
