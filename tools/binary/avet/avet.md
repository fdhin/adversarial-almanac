---
name: "avet"
categories:
  - blackarch-binary
version: "560.6da43bf"
homepage: "https://github.com/govolution/avet"
packaged-by: [blackarch]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: GPL3
meta-checked: "2026-11-09"
github-repo: "govolution/avet"
github-stars: 1761
github-pushed: "2025-04-23"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 4.3
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# avet

> Assembles antivirus-evading Windows executables from shellcode, EXE or DLL payloads.

## What it's for

AVET (AntiVirus Evasion Tool) builds Windows EXE payloads that wrap an arbitrary input — msfvenom shellcode, or an EXE/DLL converted to shellcode — in layers of evasion: XOR/RC4/avetenc encryption, sandbox and environment checks (existing files, domain membership, CPU core count, default browser, VM artifacts), downloaders (powershell, bitsadmin, certutil, curl, sockets), process hollowing, shellcode and DLL injection, and Windows-service builds for use with metasploit's psexec module. It is a toolbox of cross-compilation build scripts (wine with tdm-gcc/mingw on Linux) plus a wizard that walks a script line by line; no single technique evades every engine, so builds must be tested against the detectors that matter. Upstream states the tool is no longer being developed.

*No corpus evidence — derived from upstream documentation.*

## Typical use cases

### Build a payload with the interactive wizard

Step through a preconfigured build script and override the listener settings on the fly.

```
python3 avet.py
```

Outcome: lists every script in `build/`; after choosing one, each script line is offered for editing (typically LHOST/LPORT for msfvenom) and the modified script runs once, leaving the compiled Windows executable in `output/` — edits are temporary and do not persist to the script on disk.

### Run a build script directly

Reproduce a known-good evasion recipe without the wizard.

```
./build/build_fopen_mtrprtrxor_revhttps_win64.sh
```

Outcome: run from the avet directory, cross-compiles a win64 EXE embedding an XOR-encrypted meterpreter reverse-HTTPS payload behind fopen and gethostbyname sandbox checks; default LHOST/LPORT values come from `build/global_connect_config.sh` and can be overridden inside the script.

### Generate adversarial samples against ML detectors

Mutate a built EXE so static machine-learning detectors misclassify it.

```
gen_adversarial_exe section_injection output/payload.exe
```

Outcome: applies the named functionality-preserving manipulation (`full_dos`, `extend`, `shift`, `padding`, `section_injection`) and writes a modified sample; run it inside the Python environment set up from the repository's requirements.txt, with goodware samples placed in `input/goodware_samples` for the genetic optimizer.
