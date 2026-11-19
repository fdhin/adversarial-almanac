---
name: "reaver"
categories:
  - 802-11
version: "1.6.6-2"
homepage: "https://github.com/t6x/reaver-wps-fork-t6x"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali]
arch-builddate: "2026-04-09"
build-system: autotools
license: GPL2
meta-checked: "2026-11-09"
github-repo: "t6x/reaver-wps-fork-t6x"
github-stars: 2001
github-pushed: "2025-10-31"
github-archived: false
github-issues: 24
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.4
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# reaver

> Online WPS PIN brute-forcer (t6x community fork) that recovers a router's WPS PIN and then its WPA/WPA2 passphrase — with the offline Pixie Dust attack bolted on.

## What it's for

reaver attacks the WPS registrar PIN design flaw described by Stefan Viehböck in 2011: because the 8-digit PIN is checked in halves and has a checksum, only ~11,000 guesses are needed, and a successful final transaction makes the AP disclose its WPA/WPA2 passphrase. The packaged t6x fork (v1.6.x) is the maintained community edition: it adds the offline Pixie Dust attack (`-K`/`-Z`, delegating to pixiewps), lockout handling, session save/restore, and `wash`, the companion scanner that lists WPS-enabled APs and their chipset vendor so you can pick pixie-dust-vulnerable targets first. It needs root, a monitor-mode interface that can inject, and a target whose WPS is unlocked — the online attack takes 4-10 hours on average against a compliant AP, and longer against one that rate-limits or locks (which most modern firmware does, defeating the attack in practice).

## Typical use cases

### Run the standard online WPS PIN attack

Brute-force the PIN transaction by transaction.

```
reaver -i wlan0mon -b 00:11:22:33:44:55 -vv -c 6
```

Outcome: associates with the target and starts walking PIN guesses, printing each attempt at double verbosity; on success the WPS PIN and the recovered WPA passphrase are both printed and the session file is written so an interrupted run resumes on rerun — `-i`/`-b` plus `-vv` and the target's `-c` channel are the four flags practically every invocation carries.

### Try the offline Pixie Dust attack first

Skip the hours of online guessing when the chipset is vulnerable.

```
reaver -i wlan0mon -b 00:11:22:33:44:55 -c 6 -K -vv
```

Outcome: captures one WPS exchange and hands PKE/PKR/E-Hash1/E-Hash2/E-Nonce/Authkey to pixiewps for an offline PIN crack — seconds to minutes against vulnerable Ralink/Broadcom/Realtek firmware (do not combine with `-S` against Realtek); when it prints a PIN, finish with `reaver -i wlan0mon -b 00:11:22:33:44:55 -p <pin>` to obtain the WPA passphrase.

### Survey WPS-enabled targets with wash

Find candidates and their chipset vendor before committing.

```
wash -i wlan0mon
```

Outcome: channel-hops and lists every WPS-enabled AP in range with BSSID, ESSID, channel, signal, WPS version/lock state and a Vendor column — the vendor tells you whether the Pixie Dust attack is plausible before you start; `-j` emits the extended WPS info (model, serial) as JSON for scripting, `-a` includes non-WPS APs too.

### Keep attacking through lockouts and pacing controls

Tune delay and lock behavior against rate-limiting APs.

```
reaver -i wlan0mon -b 00:11:22:33:44:55 -c 6 -d 2 -l 120 -L -vv
```

Outcome: spaces PIN attempts 2 seconds apart, waits 120 seconds whenever the AP reports a WPS lock, and `-L` presses on regardless of the reported locked state — the levers for stubborn targets; `-g <n>` caps total attempts for a time-boxed run and `-s <file>` restores a previous session.
