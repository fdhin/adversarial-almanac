---
name: "vncrack"
categories:
  - blackarch-cracker
version: "1.21"
homepage: "http://194.150.168.69/vncrack/"
packaged-by: [blackarch]
blackarch-builddate: "2024-11-01"
build-system: make
license: GPL
meta-checked: "2026-11-09"
worth: 0.4
worth-basis: "adoption-only (no upstream data)"
worth-checked: "2026-11-09"
---

# vncrack

> Phenoelit's VNC password cracker: online dictionary attack, instant password-file decryption, and offline cracking of sniffed handshakes.

## What it's for

VNCrack (FX of Phenoelit) recovers VNC passwords three ways. It performs a dictionary attack against a live VNC server's DES challenge-response authentication, it decrypts the obfuscated password stored in a Unix `~/.vnc/passwd` file in one shot, and it cracks challenge/response pairs captured off the wire by a sniffer such as PHoss — including the MS-Logon variant used by Windows VNC servers. A fourth mode interactively decrypts the hex password blob stored in the Windows registry by WinVNC. It targets classic VNC authentication, whose DES key is limited to 8 significant characters, so any recovered password is at most 8 characters; it does not speak modern security types such as VeNCrypt/TLS.

## Typical use cases

### Dictionary-attack a live VNC server

Point it at a host and a wordlist; it walks the authentication handshake for each candidate.

```
vncrack -h target.example.com -p 5900 -w wordlist.txt -v
```

Outcome: connects to the server, answers each challenge with every wordlist entry as the DES key and prints the plaintext password when a response matches; use `-D N` to sleep N seconds between attempts on targets that throttle connections, or `-s N` to wait after connect failures.

### Decrypt a local VNC password file

Recover the stored password of a VNC server you have file access to, with no network activity.

```
vncrack -C ~/.vnc/passwd
```

Outcome: reads the DES-obfuscated password file and immediately prints `VNC password: <cleartext>`.

### Crack a sniffed VNC challenge/response offline

Feed a captured handshake pair instead of touching the server again.

```
vncrack -c <challenge-hex> -r <response-hex> -w wordlist.txt
```

Outcome: replays the dictionary attack offline against the 16-byte hex challenge and response (the format PHoss prints) and reports the matching password; for intercepted MS-Logon (UltraVNC) handshakes use `-x`/`-y` instead of `-c`/`-r`.

### Recover a Windows WinVNC registry password interactively

Enter the registry value byte by byte when you only have a hex dump.

```
vncrack -W
```

Outcome: prompts for the password value one hex byte per line (from `HKCU\Software\ORL\WinVNC\Password` or `HKU\.DEFAULT\...`) and prints the decrypted password after the last byte.

*No corpus evidence — derived from upstream documentation.*
