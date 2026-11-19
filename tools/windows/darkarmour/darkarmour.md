---
name: "darkarmour"
categories:
  - blackarch-windows
version: "4.f10228a"
homepage: "https://github.com/bats3c/darkarmour"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-17"
build-system: windows
license: MIT
meta-checked: "2026-11-09"
github-repo: "bats3c/darkarmour"
github-stars: 891
github-pushed: "2020-04-13"
github-archived: false
github-issues: 8
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# darkarmour

> Windows AV-evasion builder: encrypt a PE or raw shellcode, then generate a loader that decrypts and executes it entirely in memory so the plaintext never touches disk.

*No corpus evidence — derived from upstream documentation.*

## What it's for

Defeats signature-based detection of a payload's bytes. You hand it an existing Windows executable (or an `msfvenom -f raw` shellcode file); it encrypts the payload — XOR, optionally several rounds with a random or supplied key — and cross-compiles a new executable (via MinGW) that carries the ciphertext, decrypts it in memory at runtime and executes it there: as its own process, inside another process through reflective DLL injection, or through alternate PE loaders (jmp-based, runpe). Extra layers like UPX packing change the loader's layout further. It runs on your Linux box and produces a Windows binary; it needs an already-working payload and evades static signatures, not behavioral detection of what the payload then does.

## Typical use cases

### Generate an encrypted in-memory version of a PE

```
darkarmour -f payload.exe --encrypt xor --loop 5 -o legit.exe
```

Outcome: XOR-encrypts `payload.exe` five rounds deep (fresh key per round), embeds it in a generated loader and writes `legit.exe` — a Windows binary whose payload only ever exists decrypted in memory at execution time. Without `-o` the output gets a random filename; without `-k` the key is randomly generated.

### Wrap raw shellcode instead of a PE

```
darkarmour -S shellcode.bin --encrypt xor -o carrier.exe
```

Outcome: builds `carrier.exe` carrying the raw-format shellcode encrypted inside it, decrypting and executing it from memory — use it when the payload is `msfvenom -f raw` shellcode rather than a standalone executable.

### Run the payload inside another process

```
darkarmour -f payload.exe --encrypt xor -d -o legit.exe
```

Outcome: the generated binary uses reflective DLL injection to execute the encrypted payload inside another process instead of its own, so the malicious code never appears as its own image on the target.

### Add a UPX layer to the loader

```
darkarmour -f payload.exe --encrypt xor -u -o legit.exe
```

Outcome: after encryption the generated executable itself is packed with UPX, altering its size, entropy and section layout to break static signatures of the loader as well as the payload.
