---
name: "wifisting"
categories:
  - wireless
version: "git (2026-10-14)"
homepage: "https://github.com/yslxotwod/wifisting"
origin: "kitploit"
recipe: "upstream"
packaged-by: []
build-system: make
meta-checked: "2026-11-09"
github-repo: "yslxotwod/wifisting"
github-stars: 31
github-pushed: "2026-10-14"
github-archived: false
github-match: "exact"
gh-checked: "2026-11-09"
github-commits-year: 44
gh-activity-checked: "2026-11-09"
worth: 6.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
license: see repository
github-issues: 0
---

# wifisting

> GTK4 wireless security toolkit: 57 standalone shell modules for Wi-Fi, Bluetooth, RF, router and MITM work behind one CLI menu and GUI.

## What it's for

wifisting bundles 57 modules — Wi-Fi recon and attacks (handshake/PMKID capture, WPS, WEP, evil twin, karma, KRACK/Kr00k, WPA3 downgrade), Bluetooth/BLE work, Zigbee/Thread, SDR/sub-GHz/LoRa/NFC, router-side exploitation (TR-064, HNAP, UPnP, recovery TFTP, UART), and network MITM (ARP, rogue DHCP, Responder/mitm6, NTLM relay) — behind two frontends that share state: a numbered CLI menu (`wifisting.sh`) and a native GTK4 GUI (`wifisting-gui`, ~15k lines of C over GTK4/VTE). Every module is a standalone shell script that wraps the specialist tools (aircrack-ng, mdk4, hcxdumptool, Responder, mitm6) rather than reimplementing them, each carries a `--selftest` that runs offline, and hardware-gated modules name the missing hardware instead of returning empty results — the project's stated design goal is telling you when an attack did NOT work. Results land under `loot/` per module. Attack modules need an injection-capable adapter (Atheros AR9271-class; Intel cards are detected and refused); it is early-development software honestly labeled as tested on one machine, and the Bluetooth/SDR/Zigbee modules are written but not yet hardware-validated.

## Typical use cases

### Check module availability without root

See what the toolkit can do on this machine before touching hardware.

```
bash wifisting.sh --list
```

Outcome: prints the full module index with per-module availability (present dependencies, missing hardware) without needing root — use it to decide what is runnable before an engagement.

### Drive the toolkit from the menu

Run the interactive numbered menu.

```
sudo bash wifisting.sh
```

Outcome: opens the categorized numbered menu (system, Wi-Fi, MITM, Bluetooth, RF, analysis); picking a module elevates per-action via pkexec, and targets selected here are shared with the GUI through `/tmp/wifisting/` state — captured handshakes, credentials and other results accumulate under `loot/<module>/`.

### Run one module directly as a script

Use a module from the shell or your own automation, skipping both frontends.

```
./modules/02_recon.sh --help
```

Outcome: prints the module's own usage — every module under `modules/` is a self-contained script with its own arguments (e.g. `./modules/57_thread.sh --analyze capture.pcap`), so single steps can be scripted while the GUI/CLI remain views over the same scripts.

### Verify a module offline

Run a module's built-in self-test when output looks wrong.

```
./modules/03_attack.sh --selftest
```

Outcome: exercises the module's logic with no hardware attached and no packets sent, reporting missing dependencies or internal breakage — the documented first diagnostic step when a module misbehaves.

*No corpus evidence — derived from upstream documentation (commands verified against the yslxotwod/wifisting README).*
