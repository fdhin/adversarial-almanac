---
name: "peass"
categories:
  - blackarch-scanner
version: "20261024.8bdcb412.r0.g1c08940"
homepage: "https://github.com/carlospolop/PEASS-ng"
packaged-by: [blackarch, kali]
blackarch-builddate: "2026-10-31"
build-system: scripts
license: MIT
meta-checked: "2026-11-09"
github-repo: "carlospolop/PEASS-ng"
github-stars: 20778
github-pushed: "2026-11-07"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 8.8
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 482
gh-activity-checked: "2026-11-09"
---

# peass

> Privilege-escalation enumeration suite: LinPEAS for Linux/macOS, WinPEAS for Windows.

## What it's for

PEASS-ng (Privilege Escalation Awesome Scripts SUITE) is a family of local privilege-escalation enumeration tools by Carlos Polop. LinPEAS is a POSIX-shell script (`/bin/sh`-compatible) that sweeps a Linux/Unix/macOS host for possible local privilege escalation paths — misconfigurations, credentials, sudo rules, SUID binaries, kernel-specific issues — and prints them color-coded, with the strongest candidates highlighted in red/yellow. WinPEAS ships as .NET executables (x86/x64/anyCPU, plain and obfuscated builds) plus a `.bat` fallback for locked-down hosts. The scripts only enumerate: they surface and explain likely escalation paths but never exploit them. Three linpeas builds exist — `linpeas.sh` (default), `linpeas_fat.sh` (all checks plus embedded third-party tools) and `linpeas_small.sh` (essentials only). The `peass` package installs `linpeas`/`linpeas_fat` commands and the winPEAS binaries under `/usr/share/windows/peass/`.

## Typical use cases

### Full local enumeration on a Linux host

Run every check and capture the colored output for review.

```
./linpeas.sh -a > /tmp/linpeas.txt
less -r /tmp/linpeas.txt
```

Outcome: after roughly 5-10 minutes writes the full report to a file; `less -r` re-renders the ANSI colors so red/yellow-flagged lines (the highest-probability escalation paths) stand out. `-a` adds the slower checks — one minute of process monitoring, deeper hash hunting and per-user `su` brute force with the top 2000 passwords — which is noisy, so omit it where stealth matters.

### Run only selected checks

Keep long-running audits targeted by naming check groups.

```
./linpeas.sh -o software_information,interesting_files,api_keys_regex
```

Outcome: executes only the listed check groups instead of the whole sweep and prints just those sections — a much faster, smaller report for repeat audits.

### Stealthy fast pass

Skip time-consuming checks and write nothing to disk.

```
./linpeas.sh -s
```

Outcome: superfast/stealth mode bypasses the slow checks and creates no files during the run, trading completeness for speed and footprint.

### Enumerate an offline filesystem image

Point linpeas at a mounted directory instead of the running system, e.g. extracted firmware.

```
bash ./linpeas.sh -f /mnt/firmware-root
```

Outcome: linpeas analyzes the files under the given folder as if they were the live filesystem and reports the escalation paths it would find at boot — for targets that cannot run the script themselves.

### Enumerate a Windows host

Run the WinPEAS binary or batch fallback.

```
.\winPEASx64.exe
winPEAS.bat
```

Outcome: WinPEAS prints its color-coded Windows enumeration (services, registry autologons, scheduled tasks with creds, writable paths); the executables need .NET, the `.bat` runs everywhere else. On distro installs the binaries live under `/usr/share/windows/peass/`.

Corpus-grounded (the suite's linpeas/winpeas artifacts are the deployed surface) and verified against the upstream linPEAS and winPEAS READMEs.
