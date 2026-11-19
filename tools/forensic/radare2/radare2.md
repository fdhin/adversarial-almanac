---
name: "radare2"
categories:
  - forensics
  - hardware
  - reverse-engineering
version: "6.2.2-1"
homepage: "https://radare.org"
origin: "arch"
arch-repo: "extra"
packaged-by: [arch, kali, nixpkgs]
arch-builddate: "2026-09-09"
build-system: other
license: GPL-3.0-only AND LGPL-3.0-only
meta-checked: "2026-11-09"
github-repo: "radareorg/radare2"
github-stars: 25351
github-pushed: "2026-11-07"
github-archived: false
github-issues: 793
github-match: "search"
gh-checked: "2026-11-07"
worth: 9.0
worth-basis: "full (activity+popularity+recency+adoption)"
worth-checked: "2026-11-09"
last-refresh: "2026-11-19"
github-commits-year: 4622
gh-activity-checked: "2026-11-09"
---

# radare2

> Open-source command-line reversing framework (r2): analyze, disassemble, edit and debug binaries of nearly any architecture from one shell.

## What it's for

radare2 is a complete rewrite of radare that grew from a forensics-oriented hex editor into a full reversing framework: it opens files, block devices and kernel memory, runs analysis passes that recover functions and symbols, disassembles and graphs code, patches binaries, and debugs locally or against remote gdb/windbg servers. Day-to-day work happens in the r2 shell, where single-letter commands (a=analysis, p=print, s=seek) compose with filters and iterators; everything is scriptable through r2pipe or the embedded JS interpreter. It is the engine behind a plugin ecosystem — the extras package [[radare2-unicorn]] adds CPU emulation as a debugger backend and [[radare2-keystone]] adds the Keystone assembler, installable via the bundled r2pm package manager.

## Typical use cases

### Analyze a binary and browse its functions

Open a target, run the analysis pass, and inspect what was found.

```
r2 -A ./target
afl
pdf @ sym.main
```

Outcome: `-A` performs analysis on load; `afl` lists every recovered function and `pdf @ sym.main` prints the disassembly of main — the standard first look at an unknown binary (add `agf` for an ASCII-art function graph).

### List embedded strings with their offsets

Find the data hidden in a binary.

```
r2 -q -c iz ./target
```

Outcome: `iz` enumerates the strings in the data sections with their exact addresses and lengths — use those offsets directly as seek targets (`s <addr>`) or when patching, since the string's position in the file is what matters.

### Hexdump and patch bytes in place

Inspect and modify a binary at the byte level.

```
r2 -w ./target
px 32
```

Outcome: opens the file read-write; `px 32` prints a 32-byte hexdump of the current block, and after seeking, `w hello` writes a string at that offset — r2 reopens the file for writing with `oo+` when needed.

### Extend r2 with plugins

Pull in decompilers, arch plugins and integrations.

```
r2pm -Uci r2ghidra
```

Outcome: updates the package database and installs r2ghidra, after which the `pdg` command inside r2 decompiles the current function — `r2pm -s <word>` searches the catalog before installing.

---

## Bug bounty workflow with radare2

**Scope reality.** radare2 is a *native-code* instrument. On a bug bounty program it earns its keep the moment the scope lists something compiled: a desktop or VPN client, an installer, an Electron app's native `.node` addons, the native `.so`/`.dylib`/`.dll` libraries pulled out of a mobile app (the hand-off from [[jadx]] when Java turns into JNI), a thick client, a browser extension's native-messaging host, or firmware for an in-scope IoT device. The payouts come from secrets and logic the vendor assumed no one would read: hardcoded credentials and API keys, baked-in crypto keys, insecure auto-update endpoints, unsafe sinks reachable from attacker-controlled input, debug/backdoor accounts in firmware, and statically-linked libraries carrying known CVEs.

**The one rule that frames everything below: static analysis does not run the binary; the debugger does.** Every command in Phases 0–5 (`rabin2`, `aaa`, `pdf`, `axt`, `wa`) only *reads and rewrites bytes on disk* — radare2 never executes the target. Only Phase 6 (`r2 -d`, the `d*` family) runs code, and running an untrusted or firmware binary is running potential malware. Keep the two phases mentally separate and you will never detonate a sample on your host.

**Authorization reality.** Reverse-engineering a vendor's binary can collide with the EULA/DMCA. Most programs that put a client app or firmware in scope grant RE permission in their rules of engagement — but confirm it in writing before you decompile, and never redistribute the binary or dump large proprietary listings into a report. Keep extracted code to the minimal snippet that proves the bug.

### Phase 0 — Safe handling and provenance

Goal: get the sample onto disk in a way that is reproducible and isolated, before you touch it.

- Put the binary in its own empty directory. Treat it as untrusted data: do not run it, do not let a build tool or interpreter execute from inside that directory.
- Record identity first — a report is worthless if the triager cannot tell which build you looked at:

```
sha256sum ./client-app
rabin2 -I ./client-app        # file type, arch, bits, compiler, language, stripped?
rabin2 -K sha256 ./client-app # rabin2's own hash of the mapped image
```

`rabin2 -I` is the one-line identity card: format (ELF/PE/Mach-O), `arch`, `bits`, `endian`, `compiler`/`lang`, `stripped`, and the hardening row — `nx`, `pic`, `canary`, `relro`. Note the version string if one is embedded; you will need it for CVE matching and for the report.

### Phase 1 — Fingerprint, hardening, and dependency surface

Goal: know what you are holding and which third-party code rode along.

```
rabin2 -I ./client-app        # hardening posture (nx/canary/pic/relro)
rabin2 -l ./client-app        # linked (dynamic) libraries
rabin2 -i ./client-app        # imports — the external functions it calls
rabin2 -e ./client-app        # entrypoint(s); -ee adds constructors/destructors
rabin2 -S ./client-app        # sections;  -SS segments
```

Two things matter for payout triage. First, **statically-linked libraries are a free CVE surface** — a firmware binary that bundles an ancient zlib, OpenSSL, or libcurl is one version-string grep away from a known-CVE report (Phase 2 finds the version banner). Second, the **import list is your sink map**: `strcpy`, `sprintf`, `system`, `popen`, `exec*`, `memcpy`, `scanf` family — each import becomes an `axt` xref hunt in Phase 3.

### Phase 2 — Strings and secrets (highest ROI, zero risk)

Goal: the cheapest high-severity findings in native RE. Strings are static; this phase never runs anything.

```
rabin2 -z  ./client-app       # strings in data sections, with vaddr + paddr
rabin2 -zz ./client-app       # also strings from the whole binary (raw)
```

Then pattern-hunt for the money shapes — keys, tokens, internal URLs, credentials, private-key PEM headers:

```
rabin2 -zzqq ./client-app | grep -iE 'api[_-]?key|secret|token|passwd|password|BEGIN (RSA|EC|PRIVATE)|https?://'
rafind2 -Z -s 'BEGIN PRIVATE KEY' ./firmware.bin     # locate PEM blobs by offset
rafind2 -Z -e 'https?://[a-z0-9.-]+\.example\.com'   # regex hunt for internal hosts
```

`rafind2 -Z` prints the matched string on each hit and (with `-X`) a hexdump; `-s` is a literal string, `-e` a regex, `-x` a hex pattern, all repeatable. Every undocumented internal endpoint (`https://admin.internal.example.com`), update URL, or credential you find here is a candidate finding — but see the Pitfalls section before you call a string a "secret."

### Phase 3 — Imports → xrefs → reachable sinks

Goal: turn "this binary calls `system()`" into "this input reaches `system()`." Load analysis, list imports, then walk xrefs back to the callers.

```
r2 -A ./client-app            # -A == aaa (analyze all); -AA == aaaa (deeper)
```
```
ii                            # imports inside r2
axt sym.imp.system            # who CALLS system (xrefs TO it)
axt sym.imp.strcpy            # callers of strcpy / sprintf / memcpy / popen
s <caller-addr>               # seek to a caller
pdf                           # disassemble that function and read the data flow
```

`axt <addr|flag>` lists cross-references *to* a location (the callers); `axf` lists references *from* the current spot (what this code reaches). `axg` draws the xref graph back toward a function. The triage question at each caller: **is any argument attacker-controlled** — a network field, a filename, an environment variable, a config value parsed from an untrusted source? A `system()` whose argument is a hardcoded constant is noise; a `system()` whose format string splices a request field is command injection.

### Phase 4 — Function analysis and decompilation

Goal: understand the logic — auth checks, license validation, update verification, parsing.

```
afl                           # list all recovered functions (afl=  draws ASCII bars)
afl~crypt                     # filter the function list (r2's internal grep: ~)
s sym.check_license ; pdf     # seek + disassemble the interesting one
```

When disassembly is too dense, decompile. Built-in `pdc` gives a quick C-ish pseudocode; the real tool is **r2ghidra** (`pdg`), which runs Ghidra's decompiler in-process without the Ghidra GUI:

```
r2pm -U ; r2pm -ci r2ghidra   # one-time install
# then, inside r2, on an analyzed function:
af ; pdg                      # Ghidra decompilation of the current function
pdgo                          # pseudocode side-by-side with offsets
```

**When to escalate to full Ghidra:** r2 + r2ghidra is the fast path for one or a few functions and for scripted batch triage. Switch to the Ghidra GUI when you need whole-program cross-referencing, persistent type/struct recovery across many functions, data-type libraries, or collaborative markup — i.e., when the target is large and you will live in it for days rather than minutes. radare2 is CLI-first and scriptable; Ghidra is the heavyweight workbench. Use r2 to *find* the five functions that matter, Ghidra to *live in* them if the job is big.

### Phase 5 — Patching checkpoints (bypass PoC on your own copy)

Goal: prove a logic flaw (license/auth/signature bypass) by neutering the check. This rewrites **your local copy** — it is a proof-of-concept of client-side logic, not a change to the vendor's servers. Open write mode and edit at the opcode level:

```
r2 -w ./client-app            # open read-write
s sym.verify_signature
pdf                           # find the conditional branch that gates success
wao nop @ <addr>              # turn one instruction into NOP(s)
wa jmp 0x... @ <addr>         # or assemble a replacement branch
wx 9090 @ <addr>              # or write raw bytes
wc                            # list every pending write (the cache = your patch diff)
```

`wao` modifies an opcode in place (`wao nop`, `wao jz`→`jnz` to flip a conditional); `wa` assembles instructions from text; `wx` writes raw hex; `wc` lists the write-cache so you can show exactly what changed. Keep the original and the patched copy, then diff them for the report (Phase "Reporting evidence"). Framing matters: a client-side bypass is usually Medium unless it unlocks a *server-enforced* capability — say plainly what the patch proves and what it does not.

### Phase 6 — Dynamic validation (optional, isolated VM only)

Goal: confirm a hypothesis by running the binary — **this executes the target**, so do it only on a sample you trust or inside a disposable, network-isolated VM, and never with firmware you cannot safely boot.

```
r2 -d ./client-app            # launch under the debugger (RUNS the binary)
db sym.check_license          # set a breakpoint
dc                            # continue to it
dr                            # dump registers at the break
dm                            # memory maps
ds                            # single-step
```

The `d*` family is the debugger: `db` breakpoints, `dc` continue, `ds` step, `dr` registers, `dm` maps, `dp` process/thread. Use it to watch a decrypted secret materialize in memory, or to confirm which branch a real input takes — then go back to static analysis to write the finding. For ASLR'd, stripped, or anti-debug targets, dynamic work gets expensive fast; most bounty-grade native findings are provable statically.

### Phase 7 — Automation with r2pipe

Goal: run the same triage across dozens of firmware images or every `.so` in an APK, as a scripted check. r2pipe drives r2 from Python (and node, go, rust, ruby, …) over a pipe; `cmd()` returns raw text, `cmdj()` parses JSON command variants (the `j`-suffixed commands) into objects:

```python
# run with: python3 -I triage.py ./sample      (-I = isolate; sample is untrusted)
import sys, r2pipe
r2 = r2pipe.open(sys.argv[1])          # does NOT execute the target
r2.cmd("aaa")                           # analyze
info  = r2.cmdj("ij")                   # binary info as a dict
risky = ["sym.imp.system", "sym.imp.strcpy", "sym.imp.popen", "sym.imp.sprintf"]
for sym in risky:
    xrefs = r2.cmdj(f"axtj {sym}") or []
    for x in xrefs:
        print(f"[{info['bin']['arch']}] {sym} <- {x.get('fcn_name','?')} @ {hex(x['from'])}")
r2.quit()
```

Point this at a directory of extracted binaries and you have a fleet-wide sink census in seconds — the funnel that tells you *which* of fifty binaries is worth a human in Phases 3–5.

## Where the money is with radare2

Priority order for native bounty targets. "Reach" names the radare2 feature that gets you there; dupe risk reflects how often the same class is already reported on a mature program.

| Priority | Finding class | How radare2 reaches it | Typical severity | Dupe risk |
|---|---|---|---|---|
| 1 | Hardcoded credentials / API keys / tokens | `rabin2 -z`/`-zz`, `rafind2 -e/-s` (Phase 2) | High–Critical | High |
| 2 | Backdoor / hidden debug account (firmware) | strings + `pdg` on the auth function | Critical | Low |
| 3 | Insecure update: hardcoded/unsigned/HTTP update URL | strings + `axt` to the fetch/verify routine | High–Critical | Low–Medium |
| 4 | Hardcoded or weak crypto keys / IV / disabled cert check | strings + `axt` to crypto imports, `pdg` | High | Medium |
| 5 | Command injection sink reachable from input | `axt sym.imp.system`/`popen`/`exec*` → trace args | High | Medium |
| 6 | Memory corruption (`strcpy`/`sprintf`/`gets`/`memcpy`) | `axt` to the import, confirm attacker-sized input | High–Critical | Medium |
| 7 | Auth / license / signature-verification bypass logic | `pdf`/`pdg`, patch PoC with `wao`/`wa` | Medium–High | Low–Medium |
| 8 | Statically-linked library with a known CVE | `rabin2 -I`/version banner in strings | Medium–High | Medium–High |
| 9 | Format-string bug | `axt` to `printf`-family, check user-controlled fmt | Medium–High | Medium |
| 10 | Path traversal in a file handler | `pdg` on the path-building routine | Medium | Medium |

Phase 2 (strings) and Phase 3 (import xrefs) sit at the top because they are cheap, static, and where the fat findings cluster. Patching (Phase 5) proves logic bugs that static reading only suggests.

## Command recipes

Annotated command → expected output. radare2 analysis is static and local, so there is no rate-limit knob to worry about — the discipline here is *not executing the target* and *not over-reading proprietary code*, not request volume.

```
# Identity card — always the first command, goes straight into the report
rabin2 -I ./firmware.bin
# -> arch     mips
#    bits     32
#    canary   false
#    nx       false          <- no DX/NX: classic firmware hardening gap, note it
#    relro    no
#    stripped true
```

```
# Strings with offsets — paddr is the on-disk position you cite as evidence
rabin2 -z ./client-app
# -> [Strings]
#    nth paddr      vaddr      len ... string
#    12  0x0004a1c0 0x0044a1c0  36 ... https://update.example.com/latest   <- HTTP update?
#    13  0x0004a200 0x0044a200  40 ... apikey_live_AKIA................    <- hardcoded key
```

```
# Who can reach a dangerous sink — the xref chain is the exploitability argument
r2 -A ./client-app
[0x...]> axt sym.imp.system
# -> sym.handle_request 0x00401a30 [CALL] call sym.imp.system
[0x...]> s 0x00401a30 ; pdf     # read the caller: is the arg attacker-controlled?
```

```
# Decompile one function for the report (install r2ghidra once: r2pm -ci r2ghidra)
[0x...]> af ; pdg @ sym.check_license
# -> C-like pseudocode of the license check — paste the decisive lines into the finding
```

```
# Patch PoC on YOUR copy, then capture the exact diff
r2 -w ./client-app
[0x...]> wao nop @ 0x00401c40   # neuter the failing branch
[0x...]> wc                      # -> lists the cached write (before/after bytes)
# then diff original vs patched for the report:
radiff2 ./client-app.orig ./client-app          # byte/delta diff
radiff2 -C ./client-app.orig ./client-app       # function-level graph diff
```

```
# Batch triage across many binaries — scripted, static, no execution
python3 -I triage.py ./extracted/   # the r2pipe script from Phase 7
```

A note on analysis depth: `-A` runs `aaa` and is the right default; `-AA` runs `aaaa` (emulation-assisted, slower, finds more but also invents more false functions). On a huge or stripped firmware blob, start with `-A`, and only escalate to `-AA` on the region you care about. Inside r2, `~` is the built-in grep (`afl~login`) and `j`-suffixed commands emit JSON (`aflj`, `axtj`, `izj`) for scripting.

### Visual mode basics (when the shell isn't enough)

The CLI is the fast path, but reading dense control flow is easier with the eyes. Enter visual mode with `V`; press `q` to come back to the shell. Inside, the keys that matter for triage:

| Key | Action |
|---|---|
| `p` / `P` | rotate print modes (hex → disasm → debug → words → buf) |
| `c` / `C` | toggle cursor mode / colors |
| `V` | view the function graph (`cmd.graph`) — the call/branch structure at a glance |
| `!` | enter visual panels mode (also reachable with `v`; split with `\|` and `-`, `tab` to cycle) |
| `a` / `A` | assemble an instruction / open the visual assembler (interactive patching) |
| `i` | insert/edit hex or a string (in the hexdump view) |
| `:` | run any radare2 command without leaving visual mode (`:axt sym.imp.system`) |
| `hjkl` | move (left/down/up/right); `Enter` follows a jump/call target |

The practical loop: `V`, then `p` until you are on the disassembly panel, then `V` again to flip to the graph when the branching gets hairy, and `:` to fire off an `axt`/`pdg` without losing your place. For interactive patching, `A` (visual assembler) is the ergonomic front end to the `wa`/`wao` writes from Phase 5 — type the replacement instruction and see it land. Visual panels (`!`) give a Ghidra-like multi-pane layout (disassembly + registers + stack) when you are doing dynamic work under `-d`.

### Quick triage cheat sheet

The whole static funnel, from cold file to ranked leads, in the order you actually run it:

| Step | Command | Yields |
|---|---|---|
| Identify | `rabin2 -I file` | format, arch, hardening, version |
| Deps | `rabin2 -l file` ; `rabin2 -i file` | linked libs (CVE surface) + import sink list |
| Secrets | `rabin2 -zz file \| grep -iE 'key\|secret\|token\|https?://'` | hardcoded creds, URLs, PEM blobs |
| Analyze | `r2 -A file` | functions, xrefs, symbols |
| Sinks | `axt sym.imp.system` (and `strcpy`/`popen`/`sprintf`) | callers of dangerous functions |
| Read | `s <caller> ; pdf` or `pdg` | the data-flow into the sink |
| Prove | `r2 -w file` → `wao nop` / `wa` → `wc` → `radiff2` | patch PoC with an auditable diff |
| Scale | r2pipe `cmdj("axtj ...")` over a directory | fleet-wide sink census |

### Worked example: insecure update endpoint in a client

The highest-value native findings usually chain two cheap observations. Here is the whole arc on a hypothetical in-scope desktop client, static-only:

```
# 1. Identity + strings turn up a cleartext update URL
rabin2 -I ./updater
# -> bin  ELF64  arch x86  canary false  version 3.2.1
rabin2 -z ./updater | grep -i update
# -> 0x000b21a0 0x004b21a0  34 ... http://update.example.com/manifest.json   <- HTTP, not HTTPS

# 2. Is the URL actually used to fetch-and-run, and is the download verified?
r2 -A ./updater
[0x...]> izj~{}       # (optional) confirm the string flag name, e.g. str.http:_update...
[0x...]> axt str.http_update_example_com_manifest_json
# -> sym.fetch_manifest 0x004213a0 [DATA] lea rsi, str.http...
[0x...]> s sym.fetch_manifest ; pdg
# -> pseudocode: downloads manifest + payload over HTTP, calls apply_update()
#    with NO signature/hash check before exec — the smoking gun
[0x...]> axt sym.apply_update        # confirm reach from the download path
```

The finding writes itself: **cleartext (`http://`) update channel with no signature verification → an on-path attacker serves a malicious manifest and achieves code execution on every client that updates.** Evidence is the `rabin2 -z` offset of the URL, the `axt` chain URL → `fetch_manifest` → `apply_update`, and the `pdg` excerpt showing the missing verification. Severity Critical, dupe risk low (update-mechanism flaws are under-hunted because they require native RE). Note what you did *not* do: you never ran the updater and never contacted the real update host.

## Pitfalls, noise and scope discipline

- **Never run an untrusted sample on your host.** `r2 ./file`, `rabin2`, `aaa`, `pdf`, and `wa` are static — they are safe. `r2 -d ./file` and the `d*` debugger commands *execute* the binary. Firmware and malware samples get the debugger only inside a disposable, network-isolated VM, if at all.
- **A string is not automatically a secret.** Client apps legitimately ship *public* API keys, test fixtures, and sandbox tokens. Before you file "hardcoded credential," establish the privilege: does the key actually authenticate against a production endpoint, grant more than the client already has, or belong to a different tenant? A public publishable key is Info, not Critical.
- **A dangerous import is not a vulnerability.** `system`/`strcpy` existing in the import table proves nothing. The finding requires the `axt` chain *plus* an argument an attacker controls. Report the reachability, not the presence.
- **A local patch is client-side.** `wao nop` on a license check proves the *client* can be bypassed — which may be low impact if the server independently enforces the entitlement. State exactly what the patch demonstrates and resist inflating client-side logic into a server-side severity.
- **Decompiler output is a lead, not ground truth.** `pdg`/`pdc` can mis-recover types, stack layout, and control flow, especially on stripped or optimized code. Confirm the decisive bytes in disassembly (`pdf`) before you assert behavior in a report.
- **Respect the RE clause.** Reverse-engineering may breach the EULA unless the program's rules of engagement permit it. Get written authorization, do not redistribute the binary, and quote only the minimal proprietary snippet the PoC needs.
- **`-A` vs `-AA` false functions.** Deeper auto-analysis emulates and guesses; it recovers more but also fabricates functions and xrefs. If a finding hinges on an xref, verify it is real before reporting.

## Reporting evidence

A native-RE finding is reproducible only if the triager can land on the same bytes you did. Capture, per finding:

- **Exact sample identity:** `sha256sum` of the file and the `rabin2 -I` block (format, arch, bits, compiler, version string). Without the hash, "the binary" is ambiguous across builds.
- **Offsets, not screenshots of offsets:** the `paddr`/`vaddr` from `rabin2 -z` for a string finding; the function address and the call site from `axt` for a sink finding. Offsets are copy-pasteable and version-anchored.
- **The reachability chain:** the `axt` output showing caller → sink, plus the `pdf`/`pdg` excerpt where the attacker-controlled argument flows in. This is the difference between "calls `system`" and "command injection."
- **For a patch PoC:** the `wc` write-cache listing (before/after bytes at the address) and `radiff2 orig patched` output, so the exact modification is auditable. Keep the original file; never ship the patched binary.
- **A replayable transcript:** the sequence of r2 commands (or the r2pipe script) that reproduces the finding — the native-RE equivalent of a copy-pasteable `curl`. An `.r2` command script run with `r2 -i script.r2 ./sample` lets the triager rerun your whole triage.
- **Redact live secret values** down to a non-usable prefix in the written report, and do not paste large proprietary listings — the minimal decisive snippet only.

Severity framing, native-specific: cross-user/production hardcoded credential — High–Critical; firmware backdoor account or unsigned-update takeover — Critical; command injection or memory corruption reachable from input — High; client-side-only license/auth bypass — Medium unless it unlocks a server-enforced capability; outdated statically-linked library — severity of the underlying CVE, scoped to actual reachability. Map every finding to the program's rubric and name the boundary you did not cross.

## References

- radare2 book — command-line flags (`-A`/`-AA`, `-w`, `-q`, `-c`, `-i`, `-n`/`-nn`, `-e`, `-d`, `-S` sandbox) — https://book.rada.re/first_steps/commandline_flags.html
- radare2 book — code analysis (`aa`/`aaa`/`aaaa`, `af`, `afl`, `ax`/`axt`/`axf`/`axg`) — https://book.rada.re/analysis/code_analysis.html
- radare2 book — seeking (`s`, `so`, `sf`) — https://book.rada.re/commandline/seeking.html
- radare2 book — writing/patching (`w`, `wa`, `wao`, `wx`, `wc`, `wz`, `wo`) — https://book.rada.re/commandline/write.html
- radare2 book — print modes (`px`, `pd`/`pdf`, `pc`) — https://book.rada.re/commandline/print_modes.html
- radare2 book — visual mode (`V`, `p`/`P`, `c`, `a`/`A`, `:`, `!`/panels, `hjkl`) — https://book.rada.re/visual/intro.html
- radare2 book — visual panels — https://book.rada.re/visual/visual_panels.html
- radare2 book — debugger (`-d`, `db`, `dc`, `dr`, `ds`, `dm`, `dp`) — https://book.rada.re/debugger/intro.html
- radare2 book — rabin2 (`-I`, `-z`/`-zz`, `-i`, `-s`, `-l`, `-e`, `-R`, `-S`, `-K`) — https://book.rada.re/tools/rabin2/intro.html
- radare2 book — rabin2 symbols (`-s`/`-sr`) and libraries (`-l`) — https://book.rada.re/tools/rabin2/symbols.html , https://book.rada.re/tools/rabin2/libraries.html
- radare2 book — rafind2 (`-s`, `-S`, `-e`, `-x`, `-X`, `-z`, `-Z`, `-m`) — https://book.rada.re/tools/rafind2/intro.html
- radare2 book — radiff2 (`-A`/`-AA`, `-C`, `-g`, `-s`, `-t`, `-d`, `-j`) — https://book.rada.re/tools/radiff2/intro.html
- radare2 book — r2pipe (`r2pipe.open`, `cmd()`, `cmdj()`) — https://book.rada.re/scripting/r2pipe.html
- r2ghidra — install (`r2pm -ci r2ghidra`) and `pdg`/`pdgo` — https://github.com/radareorg/r2ghidra

*Research supplement added 2026-11-15, grounded in the official radare2 book (book.rada.re: commandline_flags, code_analysis, seeking, write, print_modes, visual, debugger, rabin2, rafind2, radiff2, r2pipe) and the r2ghidra repository (github.com/radareorg/r2ghidra); flags verified against those pages (model: claude-opus-4-8).*
