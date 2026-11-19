---
name: "exe2hex"
categories:
  - blackarch-disassembler
version: "1.5.1.r6.ge563b35"
homepage: "https://github.com/g0tmi1k/exe2hex"
packaged-by: [blackarch, nixpkgs]
blackarch-builddate: "2026-01-12"
build-system: scripts
license: MIT
aliases: ["exe2hexbat"]
meta-checked: "2026-11-09"
github-repo: "g0tmi1k/exe2hex"
github-stars: 319
github-pushed: "2020-04-27"
github-archived: false
github-issues: 0
github-match: "exact"
gh-checked: "2026-11-07"
worth: 2.9
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
github-commits-year: 0
gh-activity-checked: "2026-11-09"
---

# exe2hex

> Convert a Windows executable into ASCII text that rebuilds itself on the target using only built-in DEBUG.exe or PowerShell.

## What it's for

exe2hex encodes a PE binary into plain-ASCII batch/PowerShell files so it can be moved over a text-only channel — pasting echo lines into a shell beats transferring binary data when no file transfer exists. Executing the generated file on the target reassembles the original executable using tools Windows ships by default: the DEBUG.exe batch method covers legacy 32-bit Windows (XP/2000 era), while the PowerShell method covers x86 and x64. The input can optionally be UPX-compressed first, the output URL-encoded, and each line wrapped with prefix/suffix text; Expect scripts can be generated to automate the paste over Telnet or winexe sessions.

## Typical use cases

### Convert a binary to both dropper formats

Produce DEBUG.exe-batch and PowerShell rebuilders in one pass.

```
exe2hex -x program.exe
```

Outcome: writes `program.bat` (DEBUG.exe method, x86) and `program.cmd` (PowerShell method, x86/x64) next to the input; pasting either file's lines into a Windows shell rebuilds and executes the original program.

### Compress before converting

Shrink the payload so fewer lines need to be pasted.

```
exe2hex -x nc.exe -b /var/www/html/nc.txt -cc
```

Outcome: clones and compresses the input (reporting strip and UPX savings per step) and writes only the batch output to the given path.

### Read from a pipe

Convert without an intermediate file.

```
cat whoami.exe | exe2hex -s -b debug.bat -p ps.cmd
```

Outcome: reads the binary from STDIN (`-s`) and writes both output formats under the given names.

### Wrap lines for injection contexts

Make each output line survivable in a constrained channel.

```
exe2hex -x payload.exe -p payload.cmd -e -r "echo " -f ">>p.hex"
```

Outcome: URL-encodes the hex (`-e`) and prefixes/suffixes every generated line with the given text — here producing lines that append to `p.hex` when pasted through a command-injection or similar vector.

### Automate the paste over Telnet or winexe

Generate an Expect driver instead of pasting by hand.

```
exe2hex -x payload.exe -b payload.bat -t
```

Outcome: writes `payload.bat` plus an Expect script (`payload-bat-telnet`) that logs in over Telnet, finds a writable folder, and feeds the lines one at a time; run it as `expect payload-bat-telnet` — the script itself takes `<ip> <username> <password>` arguments (`-w` produces the winexe variant instead).

*No corpus evidence — derived from upstream documentation.*
